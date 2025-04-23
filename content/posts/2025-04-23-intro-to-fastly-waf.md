---
title: Introduction to Fastly's Next-Gen WAF
description: Overview Fastly's Next-Gen WAF
date: 2025-05-08 02:30:00 -0700
authors: [jsantos]
image: "/personal-blog/images/fastly-waf-intro-hero.jpg"
tags: [fastly, WAF, CDN, Next-Gen WAF, Application Security, DevSecOps, Bot Protection, Site Reliability Engineering, Terraform, Infrastructure as Code, IAC]
tags_color: '#9bf542'
featured: false
toc: true
---
# Fastly's Next-Gen WAF: Real-Time Protection for Real-World Traffic

When it comes to Web Application Firewalls (WAFs), most of the ones I've worked with feel like black boxes—limited control, rigid rules, and no real visibility into what’s actually happening. That’s been my experience with a few big-name WAFs. But then I found [Fastly’s Next-Gen WAF](https://docs.fastly.com/en/ngwaf/), a solution built with developers in mind. It gives you real-time insight, flexible request handling, and the kind of control you wish every security tool offered—without adding more operational overhead.

## Built to Be Smart, Not Just Strict

Out of the box, Fastly gives you sensible defaults that catch the obvious stuff—like SQL injection or path traversal. But where it really shines is how it handles gray areas. You can customize rules to fit your app’s needs and let the WAF decide what to do based on context:

- Too many requests from one IP? Rate limit it.
- Looks like a known scanner? Tag and log it.
- Payload looks weird but not malicious? Maybe allow it and flag for review.
- Confirmed bad actor? Block and move on.

Everything’s configurable. No deployments needed. Just toggle the actions and go.

## Bot Protection

Fastly's bot protection solution offers a powerful way to manage automated traffic by detecting and mitigating bots directly at the edge of its network, well before requests reach your backend. This proactive approach provides detailed visibility into bot activity and enables you to enforce custom rules using the Next-Gen WAF control panel. With features like client fingerprinting, dynamic and interactive client challenges, and advanced client-side detection, Fastly empowers you to identify and stop malicious bots—such as those attempting credential stuffing or using headless browsers—while still allowing good bots to interact with your services.

The platform gives you flexibility by letting you choose the right mix of challenge types, whether that's browser-based proof-of-work, CAPTCHAs, or automated selection based on threat level. Verified Bots support lets you accurately permit recognized bots critical to your business. There’s a lot more to explore when it comes to advanced bot management and real-world applications, so keep an eye out for a future post where we'll dive deeper into how Fastly’s bot protection can benefit your security posture.

## Example 

In the examples below, we’ll walk through how to define a WAF rule using both Fastly’s Next-Gen WAF UI to block incoming requests from a specific IP address. We’ll then use Fastly WAF's Terraform provider to create the same rule. This gives you an idea of how to implement both methods. 

Here's what the rule looks like in the UI
![](/personal-blog/images/fastly-waf-intro-example-rule.jpg)

Now let’s dive into how to codify this same policy using Terraform.
```terraform
# Define a custom tag to identify requests from known bad IP addresses
resource "sigsci_site_signal_tag" "bad_ip" {  
  site_short_name = sigsci_site.my_cool_site.short_name  
  name            = "a-bad-ip"  
  description     = "Request from a bad IP address"  
}

# Create a rule to block and tag requests from a bad IP address
resource "sigsci_site_rule" "sigsci_ip" {  
  site_short_name = sigsci_site.my_cool_site.short_name  
  reason          = "Block requests coming from a bad IP address."  
  type            = "request"  
  enabled         = true  
  expiration      = ""  
  group_operator  = "all"  
  requestlogging  = "sampled"
  conditions {
    type     = "single"
    field    = "ip"
    operator = "equals"
    value    = "1.2.3.4"
  }
  actions { 
    type          = "block"  
    response_code = 406  
  }
  actions {
    type   = "addSignal"
    signal = sigsci_site_signal_tag.bad_ip.id
  }
}
```
This configuration defines a rule in Signal Sciences to block traffic originating from a specific IP address. It begins by tagging bad behavior with the signal `a-bad-ip`, which provides a consistent way to categorize suspicious traffic. The rule then explicitly checks if the incoming request comes from IP `1.2.3.4`, and if it does, it blocks the request with a `406 Not Acceptable` response. This helps reduce threat exposure early, keeping malicious requests from ever reaching your application layer. The setup is modular, auditable, and written to support future expansion with minimal changes.

---

This isn’t just another checkbox in your compliance list. It’s a WAF that actually helps you respond to what’s happening now. You’re not writing regex and hoping for the best—you’re operating with real-time feedback and real-time control. If you’re deploying at the edge, Fastly’s WAF deserves a spot in your toolbox.