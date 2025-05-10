1. What is webhook?
- A webhook is a lightweight, event-driven communication that automatically sends data between applications via HTTP.
2. How are Webhooks work?
- To set up a webhook, the client gives a unique URL to the server API and specifies which event it wants to know about. After that, the server will automatically send the relevant payload to the client’s webhook URL when the specified event occurs, the server sends the client a single HTTP POST request as soon as the data is available.
- They are most common in SaaS and PaaS platforms like GitHub, Shopify, Stripe, Twilio because they support different types of events based on the activities that happen within them.
# Consuming a webhook
- Webhooks are regular HTTP requests and should be handled as such. 
- The HTTP endpoint must support the HTTP method used by the provider and the request's content type. 
- Payloads most commonly use a JSON, form-encoded, or XML content type, which most HTTP servers support.
??? There are several things to consider to consume webhooks successfully and avoid integrity issues

# Delivery guarantees & idempotency
- Webhooks typically have "at-least-once" delivery guarantees, which means receiving the same request more than once is possible. You should make your processing idempotent.

# Timeouts
- Webhook requests have a timeout for how long they wait for a response from your server. The timeout is typically short, at most 5 seconds and sometimes as low as 1 second. It is best practice to return an HTTP status code of 200 to confirm that you have received it.

# Throughput control
- Webhooks sent by providers are not throughput controlled, and chances are you will eventually receive more requests than you can handle. To not lose any data, you should persist the data immediately, generally using a message queue and process the event asynchronously.

# Asynchronous processing
- Because of the short timeout and lack of throughput control, webhooks should be processed asynchronously. By processing asynchronously, you can ensure that your system can respond quickly to the request and defer any long-running processing to a background job.

# Ordering
- Webhooks are not guaranteed to be delivered in order and typically include a timestamp in the payload or headers. That timestamp can be used to determine if an event is out of order or "stale" to avoid processing it.

# Security:
- Most webhook-enabled apps add a secret key to the request header of the payload, so that the client can confirm the server’s identity. 
- Webhooks are often protected with Mutual Transport Layer Security (mTLS) authentication, which verifies both client and server before the payload is sent.
- It is also common for client apps to use SSL encryption for the webhook URL, to ensure the transferred data remains private.
# Characteristist:
- Eliminate the need for polling: this saves resources for the client application
- Are quick to set up
- Automate data transfer: the payload is sent as soon as the specified event happens on the server app. This exchange is initiated by the event, so it happens as quickly as the data can be transferred from server to client—as real-time as any data transfer can be.
- Are good for lightweight, specific payloads: webhooks rely on the server to determine the amount of data it sends, leaving it to the client to interpret the payload and use it in a productive way. Since the client does not control the exact timing or size of the data transfer, webhooks deal with small amounts of information between 2 endpoints, often in the form of a notification.
3. Webhooks for infrastructure automation
# What is Infrastructure as Code (IaC)? 
- Infrastructure as Code (IaC) is the managing and provisioning of infrastructure through code instead of manual processes. Version control is an important part of IaC, and configuration files should be under source control just like any other software source code file. Deploying infrastructure as code also means that infrastructure can be divided into modular components that can then be combined in different ways through automation.
- Automating infrastructure provisioning with IaC means that developers don’t need to manually provision and manage servers, operating systems, storage, and other infrastructure components each time they develop or deploy an application. Codifying infrastructure provides a template to follow for provisioning. This can still be accomplished manually, but these processes can be automated with an enterprise-level desired state engine, such as Red Hat® Ansible® Automation Platform.
# What is GitOps?
- Often considered an evolution of IaC, GitOps is a strategic approach to managing infrastructure and application configurations using Git, an open source version control system. Following GitOps practices, developers use Git as a single source of truth for declarative infrastructure and applications, and use Git pull requests to automatically manage infrastructure provisioning and deployment. The Git repository contains the entire state of the system so that the record of changes is visible and auditable. 
# Where do webhooks come into it?
- Webhooks reduce the steps required to implement and manage git-centric deployment pipelines, and can be used to automatically launch entire IaC workflows. In a GitOps environment—with a git repository as the source of truth—a webhook functions the same as it does between 2 applications; when triggered by a specified event, 1 API sends a payload to another API. The difference lies in what type of event triggers the webhook and what the recipient does with the payload.

- In this context, the git repository plays the role of the server app, while the desired state engine—which manages the state of infrastructure—plays the role of the client app. Webhooks can be used to notify the desired state engine every time a change is made in the git repository. If a piece of code is updated and pushed to the repository, this event will trigger the webhook. The repository then automatically sends the payload to the desired state engine’s webhook address, informing it of the code change.

- If the desired state engine supports automation, these webhooks can also launch IaC workflows—turning a code change into automated action. For example, system administrators can set up automation that runs whenever a webhook’s payload is received, to automatically apply new code changes on their managed hosts and restore them to a default state. This method of using webhooks to trigger automation can be extended to perform other IT actions without human involvement, enabling a process known as event-driven automation.

- The only difference is the source of truth; instead of connecting a desired state engine to a git repository—which relies on humans to push code updates—a webhook can connect it to a third-party tool that monitors a source for specific events. Once these event sources detect a targeted event and fire the webhook, the payload can launch automation that takes immediate action to resolve the event at any time of day, without requiring IT staff to press a single button.

4. Webhooks for event-driven automation
- Event-driven automation is the process of responding automatically to changing conditions in an IT environment, to help resolve issues faster and reduce routine, repetitive tasks. 
- With event-driven automation, IT teams can codify responses to any event—like hardware issues, distributed denial-of-service (DDoS) attacks, memory shortages, or application failures—so that the necessary action is automatically executed when the event occurs.
- Event-driven solutions rely on third-party tools or plugins—like ServiceNow, Kafka, Prometheus, Sensu, Dynatrace, and Appdynamics—to monitor a source for events. Webhooks can be used to connect these event sources with an automation platform, so that when a source detects an event, the webhook triggers the appropriate automated response.
- IT teams can adopt event-driven automation incrementally to reduce mean time to resolution (MTTR) and perform functions that still require human involvement—like automatically creating a ticket—and gradually work towards fully automatic remediation, so that the appropriate action is automatically taken when a particular issue occurs.

| Tiêu chí            | WebHook                     | SSE (Server-Sent Events)      | WebSocket                  |
|---------------------|-----------------------------|-------------------------------|----------------------------|
| **Hướng giao tiếp** | Một chiều (server → client) | Một chiều (server → client)   | Hai chiều                  |
| **Giao thức**       | HTTP                       | HTTP                         | WebSocket (dựa trên TCP)   |
| **Kết nối**         | Không liên tục             | Liên tục (một chiều)         | Liên tục (hai chiều)       |
| **Độ phức tạp**     | Thấp                       | Trung bình                    | Cao                        |
| **Dữ liệu hỗ trợ**  | Thường JSON (HTTP payload) | Văn bản (thường JSON)        | Văn bản, nhị phân          |
| **Tự động reconnect**| Không                      | Có                           | Không (cần xử lý thủ công) |
| **Ứng dụng tiêu biểu** | Thông báo, tích hợp hệ thống | Cập nhật tin tức, live feeds | Chat, game, dashboard      |