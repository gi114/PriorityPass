# PriorityPass

## Architecture Overview: Your technical choices and rationale
- One of the first decisions I encountered is whether to offer a redirect from the Priority Pass (PP) to the Taxi App software for taxi booking and payment transaction, or whether to keep the user on the PP app to book a taxi. While the first approach could be possible, it would not be a great user experince given the different branding of the taxi app. The exercise is also asking for inventory sharing, so keeping all the flow inside the PP app would be better. The PP manages the whole UI, while behind the scene external APIs for Taxi booking and payments are called. Data is re-surfaced back to the modile app
- Given the PP app offers user accounts, there has to be an authentication service, likely using the Auth0 2.0 Token Exchange. A secure token that is mapped to a User ID/Secret is returned by the service after validating the credentials - this can have an expiry date or limited scope. The token is passed to the Taxi Integration Service and the Taxi API to prove who the user is. The PP API Logic orchestrates this behavior 
- For PCI Compliance, the PP app does not want to have access to credit card details, so a Payment Service would be dedicated to contact an external banking system to enable the payment, only recieving a booking confirmation token that can be provided to the Taxi API to actually schedule a taxi!
- I decided to create a Taxi Integration service, which could be a microservice, to handle the PP side of managing all Taxi details, which are fetched from the Taxi API. This modular approach is useful for mantainance and updates, and for clarity when building software. Similarly I separated the Payment Service which, like the Integration Service, talks to an external system
- I showed the Taxi Integration Service components as different parts where each component does one job only and uses separate API keys if needed. The Journey Planner is central in provided estimates and updates,supporting a notification system. The cache is used for efficiency to reduce Taxi API hits, so cost effective too

  
## PCI Compliance
- The PP Mobile app Payment Service uses a PCI-compliant SDK to collect card details input by the user, and only stores a secure token and transaction ID locally. No card details are stored in PP
- All transactions are based on TLS 1.3 ensuring encryption in transit

## Omissions and trade offs
- A way to show the taxi location real time, added complexity
- I have't followed all the packets flow in the diagram, I might have missed something
- If the taxi booking fail, the user misses the flight! need to think of multiple taxy providers
- Could have added a Notification System to only deal with user notifications
- All services in my perspective should be thought as microservices
