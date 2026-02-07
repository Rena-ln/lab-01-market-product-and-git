## Product Choice

+ Wildberries:
+ [Link to the product's website](https://www.wildberries.ru/);
+ Online markertplace.

## Product components

![Wildberries Component Diagram](../docs/diagrams/out/wildberries/architecture-component/Component%20Diagram.svg)

1. Client layer contains customer website. It is way for platform to communicate with customers
2. Client layer also contains customer mobile apps.It is tool for mobile phones to communicate with platform easily
3. Data layer contains object storage. It is the tool for saving all images and videos customer will se on the screen
4. Core domain servise contains catalog & search service. It is algorithm for looking for some exactly thing out of amount different things
5. SMS/Push Providers is mot inside Wildberries platform. It is way to inform customers about some sales not only inside the Wildberries platform

## Data flow

![Wildberries Sequence Diagram](../docs/diagrams/out/wildberries/architecture-sequence/Sequence%20Diagram.svg)

**Checkout (Reservation & Payment)**

+ When the user clicks “Pay Now,” the system calls createOrder to initialize the order. 
  
+ It first reserves stock for the items in the cart by decrementing available inventory and holding it for 15 minutes. The order is saved with a PENDING_PAYMENT status. 
  
+ Then, a payment transaction is initiated—typically involving tokenized card charging and 3DS authentication. The user is redirected to a bank 3DS page inside a WebView to confirm the payment. 

+ Once authorized, the payment callback updates the order status to PAID and publishes an OrderPaid event to notify downstream services like the warehouse management system (WMS)

## Deployment

![Wildberries Deployment Diagram](../docs/diagrams/out/wildberries/architecture-deployment/Deployment%20Diagram.svg)

**E-Commerce Pods**

1. Cart & Checkous. It allows to each customer have his own card with things he is planning to buy. This information stores in In-Memory claster
2. After it Auth and ID Service. It lets users to sign in and get personal recomendations. This information stores in In-Memory claster
3. Of course, it's necessery to have syslem of searching things. This information stores in In-Memory claster
4. And finally platform must have good Fintech & Payment system. It allows to customer to buy things online

## Open questions

**Question:** Why is the TTL for user cart data set to 7 days?

**Answer:** The 7-day TTL is typically set to balance user convenience and system resource management. It allows users to return to their cart within a reasonable timeframe without permanently storing unused data, helping to free up cache (Redis) space and keep inventory availability calculations up-to-date.


**Question:** What technology is used to transmit events between different services (Core Business Services, WMS) after the order status is updated in the database?

**Answer:** According to the diagram, Kafka Event Bus is used for asynchronous event streaming between services, such as publishing the OrderPaid event from the core service to downstream systems like the Warehouse Management System (WMS).