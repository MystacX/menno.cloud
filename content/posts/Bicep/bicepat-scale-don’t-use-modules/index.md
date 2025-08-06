---
title: "Bicep at Scale – Don’t Use Modules"
date: 2025-08-06T20:32:39+02:00
hero: bicep_at_scale-hero.png
description: Why you shouldn't use modules at scale
menu:
  sidebar:
    name: Bicep at Scale – Don’t Use Modules
    identifier: bicep-at-scale-modules
    parent: bicep
    weight: 10
author:
  name: Menno Korthoff
math: true
---

When working with **Bicep** at scale, one of the first decisions you’ll face is whether to loop over modules or go for direct resource deployments. It’s tempting to fall into the trap of looping through modules because it seems like the easier option, especially if you're deploying a large number of resources. But in reality, looping over modules is a bad idea, and you should **avoid it**.

Here's why.

#### 1. **Performance Hits**

Bicep is designed to be declarative. That means you define your resources and their relationships, and Bicep figures out the dependencies for you. When you loop over modules, Bicep treats each module as an isolated deployment, which can add significant overhead. If you have hundreds of resources, this extra layer of complexity introduces performance bottlenecks and deployment failures.

You might hit **deployment limits** faster, which can slow down or even fail your deployment. Instead, using **direct resource declarations** allows Bicep to optimize the deployment graph more efficiently, improving performance and reducing the risk of failures.

#### 2. **Excessive Complexity**

While it might feel like looping over a module gives you some abstraction, it often results in **unnecessary complexity**. The more you loop, the more complicated your code becomes, and when you hit scaling limits, you’ll spend more time debugging dependency issues than actually deploying resources.

It's easier to keep things simple with direct resource definitions. Rather than wrapping your resources in another layer, define them clearly and concisely. This is especially true when scaling. When you need to scale your deployment, changing an array size or updating a parameter is far easier than modifying a loop structure wrapped around multiple modules.

#### 3. **Risk of Misconfiguration**

With modules, you’re often working with predefined configurations. If you’ve got 50 or 100+ modules running in parallel, it becomes easy to miss something: a network configuration, an incorrect VM size, or a wrong subnet definition. When you're dealing with large environments, you need **visibility** into every resource you’re deploying. Direct resource declarations give you this clarity. No surprises, no hidden dependencies. You can see exactly what’s being deployed, and if something goes wrong, you can trace the problem quickly.

#### 4. **Scalability Challenges**

Many people assume that if they loop over modules, they can just scale as needed. But the reality is, scaling in this way introduces **more challenges than it solves**. If you're deploying 50 VMs, looping over modules might work fine, but if you need 500 VMs, suddenly the approach breaks down. This is because looping over modules doesn’t scale well past a certain point, and it makes it much harder to adjust parameters or tweak configurations in a manageable way.

With direct resource declarations, on the other hand, scaling is simple. You can use arrays or dictionaries to pass dynamic values into your resources, making it easy to scale up or down as needed without the overhead of looping through modules.

#### 5. **The Bottom Line: Keep It Simple**

At scale, the goal is **simplicity**. You're not looking for complex abstractions or more code. What you need is a streamlined deployment process that’s easy to manage and troubleshoot. Direct resource deployment allows you to:

* Scale quickly without breaking the deployment pipeline
* Improve readability and maintainability of your code
* Avoid complex dependencies and overhead
* Ensure better performance and fewer deployment issues

### Conclusion

When you’re working with Bicep at scale, **don’t use modules**. It’s just unnecessary complexity that slows you down. Use **direct resource declarations**, leverage arrays or dictionaries, and scale your deployments the simple way. The fewer layers you introduce, the easier it will be to scale and maintain your infrastructure. Keep it lean, keep it efficient.
