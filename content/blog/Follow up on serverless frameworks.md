---
title: "Follow up on Serverless Frameworks"
date: 2021-06-14
draft: false
tags: ["blog", "opinion", "aws", "api gateway", "lambda", "cdk", "iac", "chalice"]
TableOfContents: true
author: "Alex Mitelman"
---

I've received incredible feedback on my last week's blog post [Amazon API Gateway with Lambda is the Next Generation of Web Frameworks]({{< ref "/blog/Amazon API Gateway with Lambda is a next generation of web frameworks.md" >}}).

As [Adrian Mace](https://amace.id.au/) suggested, there is a new Infrastructure as Code (IaC) tool from Amazon, called AWS CDK (Cloud Development Kit). I missed this awesome tool. To quote Adrian:

> "CDK allows you to define your infrastructure using imperative languages like TypeScript, Python, or Golang with the full powers that those languages can provide, and then ‘compiles down’ into Cloudformation templates upon deploy/synth.
> 
> When it comes to learning, a great resource is Amazon’s official [CDK Workshop](https://cdkworkshop.com/). You should have a play with it and see what you think, their concept of ‘Constructs’ has sparked the creation of [https://cdk8s.io](http://cdk8s.io/) and [cdktf](https://learn.hashicorp.com/tutorials/terraform/cdktf) as well.

> In AWS CDK, The Level 1 Constructs directly map to CloudFormation resources (all the properties in the constructs set properties in the resource). Then you have Level 2 Constructs which the CDK team builds and maintains which uses one or many L1 constructs to build a ‘solution’.

> For example, [ApplicationLoadBalancedFargateService](https://docs.aws.amazon.com/cdk/api/latest/python/aws_cdk.aws_ecs_patterns/ApplicationLoadBalancedFargateService.html) which with a single object and by only defining the bare minimum (your Fargate code and a few other things) it will deploy an ECS Cluster, push your docker container to ECR, deploy a Fargate task, set up load balancing, etc. It does this by creating multiple L1 constructs with sensible defaults on your behalf.

> [More examples here](https://github.com/aws-samples/aws-cdk-examples)".

Turns out, Mischa Spiegelmock came up with very similar ideas in his recent blog post [Frameworkless Web Applications](https://spiegelmock.com/2021/05/29/frameworkless-web-applications-aws-cdk/) a couple of days before me. Even more, they created an anti-framework for building cloud-native serverless applications. So now building IaC looks and feels like a regular web framework. It's called [JetKit/CDK](https://www.jetkit.dev/). Check it out.

λ According to [The State of Serverless 2021](https://www.datadoghq.com/state-of-serverless/), Lambda functions are invoked 3.5 times more often than two years ago. The Serverless Framework is the leading way to deploy Lambda applications with AWS CloudFormation. Python is the most popular Lambda runtime, especially in large environments.

With that being said, Amazon created its own Python framework for Lambda and API Gateway applications. It's called [Chalice](https://aws.github.io/chalice/). Hello World application would look like this:

```python
from chalice import Chalice

app = Chalice(app_name="helloworld")

@app.route("/")
def index():
    return {"hello": "world"}
```

If that looks familiar, it's intentional. The syntax is very similar to Flask. Even the name "Chalice" gives away this intention. Developers created APIs like this for years. Now it is possible to create serverless applications without leaving the comfort zone.

Chalice is built on top of AWS SAM which is built on top of CloudFormation. While Chalice helps to generate Infrastructure as Code for API Gateway and Lambda, applications usually use more infrastructure than that, for example, database.

There is a tight integration between AWS CDK and Chalice. By creating `infrastructure` folder inside the project, we can define infrastructure in Python linking it to the Chalice application. CDK recognizes the app and sets appropriate IAM policies for Lambda functions to access infrastructure like a database.

Chalice creates REST API within AWS API Gateway as opposed to HTTP API. Wait, what? Yes, Amazon came up with this very confusing naming. Naming is hard. In short, HTTP API is the new version of API Gateway. Amazon claims it to be [up to 71% cheaper](https://aws.amazon.com/about-aws/whats-new/2019/12/amazon-api-gateway-offers-faster-cheaper-simpler-apis-using-http-apis-preview/) than REST API that is sort of an old version of API Gateway. So, Chalice currently supports only the old version - REST API. There is [no indication](https://github.com/aws/chalice/issues/1321) of when Chalice gets the support of HTTP API. These two versions of API Gateway have different feature sets, [more on this here](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-vs-rest.html). As Chalice is being actively developed by Amazon, and Amazon bets on serverless and IaC, there is a good chance that Chalice will get HTTP API support sooner or later.

To wrap things up, there is a growing community and interest in serverless development. The existence of [Awesome Chalice](https://github.com/chalice-dev/awesome-chalice) list on GitHub is a good indicator of that.

{{<cc>}}