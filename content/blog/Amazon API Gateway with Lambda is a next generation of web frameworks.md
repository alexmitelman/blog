---
title: "Amazon API Gateway with Lambda is the Next Generation of Web Frameworks"
date: 2021-06-01
draft: false
tags: ["blog", "opinion", "aws", "api gateway", "lambda", "iac"]
TableOfContents: true
author: "Alex Mitelman"
---

## A short history of web frameworks

What is a web framework? A web framework is a set of tools for the rapid development of server-side applications that provides a boilerplate to simplify the implementation of common tasks. [According to MDN](https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Web_frameworks), those tasks are routing URLs to appropriate handlers, interacting with databases, supporting sessions and user authorization, formatting output (e.g. HTML, JSON, XML), and improving security against web attacks. The most popular web frameworks are Ruby on Rails, Django, Flask, FastAPI, Express, Laravel.

If you are old enough to remember the world without such frameworks, you may think of a PHP project where each PHP file responsible for generating a different web page with some MySQL queries to build the dynamic content from the database. Building such websites required a lot of effort.

People quickly noticed that there is a common pattern in building different websites, so they've built frameworks to automate the boilerplate code. The motto of Django is "The Web framework for perfectionists with deadlines". Without diving into specifics of model–view–controller (MVC) paradigm, let's just acknowledge that developers get an initial folder structure, ORM to access a database without writing a single line of SQL and many more, so developers can start implementing the business logic fairly quickly. As a downside, there is a learning curve to learn another tool. Developers now need to follow some framework conventions that sometimes may feel like magic, for example passing some object references as strings in Django.

With the introduction of the single-page applications (SPA) concept, web development split into two applications: backend API that generates responses with JSON and frontend application that renders the page and makes requests to the backend over RESTful API. Backend frameworks don't need to generate HTML anymore, frontend applications implemented with React or Vue are now responsible for rendering the web page. At this point, the backend application represents an API that returns data in a form of a JSON object. Effectively, any backend application is now associated with building an API. Flask micro web framework is a great tool for building such API.

In the most common cases, implementing an API with Flask means routing a request to an appropriate handler function, authorizing the request and running other custom middleware on the request and/or response objects, validating the input, making a database query, rendering a response.

## Amazon API Gateway as a framework
So how exactly Amazon API Gateway can help us building an API? Isn't it some sort of reverse proxy that is also used to call AWS Lambda from the internet?

Well, turns out that API Gateway can do much more than that. A brilliant article [A Detailed Overview of AWS API Gateway](https://www.alexdebrie.com/posts/api-gateway-elements/) can help us to learn more about it. So let's consider this diagram that represents API Gateway flow:

![/images/blog/api_gateway_framework/api_gateway_diagram.png](/images/blog/api_gateway_framework/api_gateway_diagram.png)

(Image courtesy Alex DeBrie)

Do you recognize the pattern from our Flask API implementation? Here API Gateway also accepts the request, authorizes it, routes the request to the appropriate handler function, renders the response. Notice that both Flask and FastAPI don't provide ORM out of the box, so it's OK to rule it out of equation in case of API Gateway as well.

Remember how we can concentrate on implementing business logic in the case of Flask? Similarly, we can now concentrate on implementing our business logic in a form of Lambda function, and API Gateway can take care of everything else for us.

API Gateway can authorize the request against an identity service like Amazon Cognito or any other like Auth0. In this case, our Lambda function already accepts authorized requests with the identity data.

API Gateway can validate the request parameters and the request body according to predefined JSON schema so our Lambda function can skip validation steps.

Lambda function receives an event object with all the information about the request, the same way as web framework passes the request to our handler function.

Finally, API Gateway can transform the response from the Lambda function according to the predefined mappings, for example, return the right status code.

Same as with other frameworks, there is a learning curve. API Gateway should be configured to do its job that we won't do in our application code anymore. The best practice is Infrastructure as Code (IaS) as opposed to configuration manually by mouse clicks in the AWS console web interface. There is a variety of tools for that. [Serverless framework](https://www.serverless.com/) can be a "gateway drug" to IaS as it has the most human-readable format. [AWS Serverless Application Model](https://aws.amazon.com/serverless/sam/) (SAM) is an IaS specifically for building serverless applications with Lambda. [Terraform](https://www.terraform.io/) is another option. [AWS CloudFormation](https://aws.amazon.com/cloudformation/) is the most sophisticated solution to provision any AWS resource. Most likely that native parameters to define fine-grained APIs with Serverless, SAM, and Terraform will lack some features. In this case, they fall back to the CloudFormation format.

If you'd want to get your hands dirty with building your first serverless application, I'd suggest trying out Serverless framework as has the most friendly language. It should be enough to get you an impression of IaS and deploying serverless applications. It also allows running Lambda function locally, however, I noticed that the event format is different from the actual Lambda, so I had to debug and find out the actual event format. From another hand, it allows you to see the output and errors of your cloud functions in the terminal locally which is very helpful for debugging.

Although Serverless is nice for building very simple applications, it won't help to bring the full power of API Gateway. I'd suggest AWS CloudFormation as the last resort to ultimately configure every detail of API Gateway.

## Infrastructure as Code learning curve

This brings us to the next question. Is it worth it? Why do I need to learn another tool?

Of course, there is no right answer. It depends. There was some learning curve for your favorite web framework. Why CloudFormation should be any different? It's just a tool that serves its purpose. The bigger question is why should I make a serverless application while I can develop it with my favorite framework. And the answer is again "It depends".

I've developed a Telegram bot that makes some basic calculations for me. I am a single user of this Telegram bot. It's implemented with Lambda so I don't need to spin up and manage an entire EC2 instance that will answer my question once a week or so.

If you have some occasional workload, Lambda is a great candidate not to have an instance up and running the entire time. If your load is unpredictable, if there is a chance of going viral, Lambda is a good candidate to scale automatically for you while your EC2 instances can die under the load.

Let's not forget that provisioning those instances requires some effort too, with all those IAM permissions, VPC groups, and all that jazz. All in all, you will end up having it in a form of IaC anyway. So we break even with serverless here. Except for the burden of managing security updates is still on you while Amazon does all the job for us in case of serverless.

As an indie developer, there is a need to be a full-stack. This means that in addition to frontend and backend roles there is a need to wear a DevOps hat as well. I can imagine it's not something new, there was always a need to deploy an application somehow.

With all that being said, I imagine that DevOps become a part of a development team. While frontend and backend were responsible for the application layer, DevOps were responsible for the infrastructure layer. With some part of application responsibilities shifting to other components, there is a growing need to work closely. I can imagine a team that contains frontend, backend, and DevOps engineers working on some features together.

## Conclusion

We always build on top of existing technologies adding another layer of abstractions. We moved bytes between CPU registers with Assembly, then C allowed us to express logic in a more human-readable form. Higher-level languages allowed us to build dynamic websites. Web frameworks allowed us to concentrate on implementing the actual business logic and API. Serverless application is another level of abstraction that allows us to provision and deploy scalable web applications. The application can be written on a variety of languages supported by AWS Lambda, including Python, JavaScript, Go, Java, C#. Different parts of such applications can be developed in different languages, for example, if there are different teams in charge of different parts of an API. Debugging is going to be more challenging due to the cloud-first nature of such a system. I'm looking forward to seeing the progress in tooling for this ecosystem.

*Update. There is a [follow-up blog post on serverless frameworks]({{< ref "/blog/Follow up on serverless frameworks.md" >}})*.