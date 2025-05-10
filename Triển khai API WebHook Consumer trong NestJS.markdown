# Triển khai API WebHook Consumer trong NestJS

## Điểm chính
- **Mục đích**: Xây dựng một API trong NestJS để nhận và xử lý các sự kiện WebHook từ dịch vụ bên ngoài.
- **Cách tiếp cận**: Tạo một module riêng với controller và service để xử lý yêu cầu WebHook.
- **Bảo mật**: Xác thực payload (ví dụ: chữ ký HMAC) để đảm bảo yêu cầu đến từ nguồn hợp lệ.
- **Idempotency**: Xử lý các sự kiện trùng lặp để tránh tác động không mong muốn.
- **Độ phức tạp**: Yêu cầu cấu hình đơn giản nhưng cần chú ý đến bảo mật và xử lý lỗi.

# Hướng dẫn chi tiết triển khai API WebHook Consumer trong NestJS

## 1. Tạo một Module mới cho WebHook Handler
Sử dụng NestJS CLI để tạo một module riêng cho việc xử lý WebHook:
```bash
nest generate module webhook-handler
```

## 2. Định nghĩa Interface cho WebHook Handler
Tạo một interface để chuẩn hóa cách xử lý WebHook. File: `src/webhook-handler/webhook-handler.interface.ts`
```typescript
export interface WebhookHandler {
  handleWebhook(payload: any, signature?: string): Promise<void>;
}
```
Interface này đảm bảo service có phương thức xử lý WebHook và hỗ trợ xác thực chữ ký nếu cần.

## 3. Triển khai Dịch vụ (Service) WebHook Handler
Trong file `src/webhook-handler/webhook-handler.service.ts`, triển khai logic xử lý WebHook:
```typescript
import { Injectable } from '@nestjs/common';
import * as crypto from 'crypto';
import { WebhookHandler } from './webhook-handler.interface';

@Injectable()
export class WebhookHandlerService implements WebhookHandler {
  private readonly secretToken = process.env.WEBHOOK_SECRET || 'your-secret-token';

  async handleWebhook(payload: any, signature?: string): Promise<void> {
    // Xác thực payload nếu có chữ ký
    if (signature && !this.validatePayload(payload, signature)) {
      throw new Error('Invalid webhook signature');
    }

    // Logic xử lý WebHook
    console.log('Received webhook payload:', payload);
    // Ví dụ: Lưu vào database, gọi API khác, hoặc xử lý logic cụ thể
  }

  private validatePayload(payload: any, signature: string): boolean {
    const expectedSignature = crypto
      .createHmac('sha256', this.secretToken)
      .update(JSON.stringify(payload))
      .digest('hex');
    return crypto.timingSafeEqual(
      Buffer.from(signature.replace('sha256=', ''), 'hex'),
      Buffer.from(expectedSignature, 'hex'),
    );
  }
}
```
- **Secret token**: Lưu trong biến môi trường (`WEBHOOK_SECRET`) để bảo mật.
- **Xác thực**: Sử dụng HMAC-SHA256 để kiểm tra chữ ký, phù hợp với các dịch vụ như GitHub hoặc Stripe.

## 4. Triển khai Controller WebHook Handler
Tạo controller để nhận yêu cầu HTTP từ WebHook. File: `src/webhook-handler/webhook-handler.controller.ts`
```typescript
import { Controller, Post, Body, Headers } from '@nestjs/common';
import { WebhookHandlerService } from './webhook-handler.service';

@Controller('webhook-handler')
export class WebhookHandlerController {
  constructor(private readonly webhookHandlerService: WebhookHandlerService) {}

  @Post()
  async handleWebhook(
    @Body() payload: any,
    @Headers('x-hub-signature-256') signature: string,
  ): Promise<void> {
    await this.webhookHandlerService.handleWebhook(payload, signature);
  }
}
```
- **Endpoint**: Nhận yêu cầu POST tại `/webhook-handler`.
- **Headers**: Lấy chữ ký từ header `x-hub-signature-256` (phổ biến với GitHub).

## 5. Xuất Module WebHook Handler
Trong file `src/webhook-handler/webhook-handler.module.ts`, đảm bảo module được cấu hình đúng:
```typescript
import { Module } from '@nestjs/common';
import { WebhookHandlerController } from './webhook-handler.controller';
import { WebhookHandlerService } from './webhook-handler.service';

@Module({
  controllers: [WebhookHandlerController],
  providers: [WebhookHandlerService],
})
export class WebhookHandlerModule {}
```

## 6. Tích hợp Module vào Ứng dụng Chính
Trong file `app.module.ts`, import module WebHook Handler:
```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { WebhookHandlerModule } from './webhook-handler/webhook-handler.module';

@Module({
  imports: [WebhookHandlerModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

## 7. Cấu hình Biến Môi trường
Thêm secret token vào file `.env`:
```
WEBHOOK_SECRET=your-secret-token
```
Sử dụng package `@nestjs/config` để đọc biến môi trường:
- Cài đặt: `npm install @nestjs/config`
- Cập nhật `app.module.ts`:
```typescript
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { WebhookHandlerModule } from './webhook-handler/webhook-handler.module';

@Module({
  imports: [
    ConfigModule.forRoot(),
    WebhookHandlerModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

## 8. Xử lý Idempotency
Để tránh xử lý trùng lặp các sự kiện WebHook (phổ biến với Stripe), lưu trữ ID sự kiện trong database:
- Tạo entity cho sự kiện (ví dụ, sử dụng TypeORM):
```typescript
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class WebhookEvent {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({ unique: true })
  eventId: string;

  @Column()
  processedAt: Date;
}
```
- Cập nhật service để kiểm tra sự kiện:
```typescript
import { Injectable } from '@nestjs/common';
import * as crypto from 'crypto';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { WebhookEvent } from './webhook-event.entity';
import { WebhookHandler } from './webhook-handler.interface';

@Injectable()
export class WebhookHandlerService implements WebhookHandler {
  constructor(
    @InjectRepository(WebhookEvent)
    private webhookEventRepository: Repository<WebhookEvent>,
  ) {}

  private readonly secretToken = process.env.WEBHOOK_SECRET || 'your-secret-token';

  async handleWebhook(payload: any, signature?: string): Promise<void> {
    if (signature && !this.validatePayload(payload, signature)) {
      throw new Error('Invalid webhook signature');
    }

    const eventId = payload.eventId; // Giả sử payload có trường eventId
    const existingEvent = await this.webhookEventRepository.findOne({ where: { eventId } });

    if (existingEvent) {
      console.log('Event already processed:', eventId);
      return;
    }

    // Lưu sự kiện mới
    const webhookEvent = new WebhookEvent();
    webhookEvent.eventId = eventId;
    webhookEvent.processedAt = new Date();
    await this.webhookEventRepository.save(webhookEvent);

    // Logic xử lý WebHook
    console.log('Received webhook payload:', payload);
  }

  private validatePayload(payload: any, signature: string): boolean {
    const expectedSignature = crypto
      .createHmac('sha256', this.secretToken)
      .update(JSON.stringify(payload))
      .digest('hex');
    return crypto.timingSafeEqual(
      Buffer.from(signature.replace('sha256=', ''), 'hex'),
      Buffer.from(expectedSignature, 'hex'),
    );
  }
}
```
- Cập nhật module để sử dụng TypeORM:
```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { WebhookHandlerController } from './webhook-handler.controller';
import { WebhookHandlerService } from './webhook-handler.service';
import { WebhookEvent } from './webhook-event.entity';

@Module({
  imports: [TypeOrmModule.forFeature([WebhookEvent])],
  controllers: [WebhookHandlerController],
  providers: [WebhookHandlerService],
})
export class WebhookHandlerModule {}
```

## 9. Chạy Ứng dụng
Khởi chạy ứng dụng NestJS:
```bash
npm run start:dev
```
Ứng dụng sẽ chạy tại `http://localhost:3000` (mặc định).

## 10. Kiểm tra WebHook
Sử dụng Postman hoặc cURL để gửi yêu cầu POST đến `/webhook-handler`:
```bash
curl -X POST http://localhost:3000/webhook-handler \
-H "Content-Type: application/json" \
-H "x-hub-signature-256: sha256=your-calculated-signature" \
-d '{"eventId": "123", "someData": "example"}'
```
- Tính toán chữ ký HMAC-SHA256 cho payload để kiểm tra xác thực.
- Kiểm tra log trong console để xác nhận payload được nhận.

## 11. Thử nghiệm cục bộ với các dịch vụ cụ thể
- **Stripe**: Sử dụng [Stripe CLI](https://github.com/stripe/stripe-cli/releases/tag/v1.6.4) để chuyển tiếp sự kiện WebHook đến localhost:
  ```bash
  stripe listen --forward-to localhost:3000/webhook-handler
  ```
  Lưu trữ secret token từ Stripe CLI trong `.env`.
- **GitHub**: Cấu hình WebHook trong repository settings, cung cấp URL công khai (sử dụng [ngrok](https://ngrok.com/) để expose localhost).

## 12. Các cân nhắc bổ sung
- **Bảo mật**: Đảm bảo endpoint chỉ nhận yêu cầu từ nguồn đáng tin cậy. Sử dụng HTTPS trong môi trường production.
- **Xử lý lỗi**: Thêm cơ chế retry hoặc lưu trữ các sự kiện thất bại để xử lý sau.
- **Tái sử dụng**: Đóng gói module thành package npm nếu cần sử dụng trong nhiều dự án:
  - Tạo `package.json` với cấu hình phù hợp.
  - Chạy `npm publish` để xuất bản.
