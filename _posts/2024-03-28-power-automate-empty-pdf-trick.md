---
title: "The Empty PDF Trick That Saved My Power Automate Sanity"
date: 2024-03-28 15:30:00 -0500
categories: [Tips & Tricks, Power Automate]
tags: [power-automate, automation, microsoft, tips, custom-prompts, workflow, life-hacks]
image:
  path: /assets/img/headers/custom-prompts.jpg
  alt: Power Automate Custom Prompts
---

# The Empty PDF Trick That Saved My Power Automate Sanity 🔧

It was 4:45 PM on a Friday. I'd spent the last three hours staring at the same error message while my manager kept asking when our new approval flow would be ready for Monday's launch. The coffee on my desk had gone cold. Twice.

The culprit? Custom Prompts in Power Automate and their stubborn refusal to accept optional files.

## What Are Custom Prompts Anyway?

If you're not deep in the Power Automate trenches yet, custom prompts are Microsoft's way of letting you use GPT models in your flows. Pretty cool when they work. Intensely frustrating when they don't.

These AI-powered actions let you generate text based on specific instructions. Our team has been using them to automate content creation for our document reviews. Game changer... until they aren't.

You can read Microsoft's perfectly sanitized explanation [here](https://learn.microsoft.com/en-us/ai-builder/use-a-custom-prompt-in-flow), but let me tell you how it actually goes down in the real world.

## The Most Annoying Limitation Ever 🤦‍♂️

Here's my exact scenario: I built a custom prompt that needs two inputs — a text description and a file to analyze. Simple enough.

Except — and here's where I lost hours of my life I'll never get back — sometimes my team doesn't have a file to upload. Maybe 40% of the time, it's just text.

Should be simple, right? Just make the file optional. NOPE.

I dug through every setting, checked every option, and even tried convincing myself I was missing something obvious. Three cups of coffee later, the truth was inescapable: custom prompts treat every input as mandatory. Every. Single. One.

When you try running the flow without a file, you get this lovely gem:

![Bad request error in Power Automate](/assets/img/posts/badrequest.png)

Error in the application? YOU THINK? My first instinct was to file a bug report. My second was to throw my laptop out the window. I did neither.

## The Empty PDF Hack (Because That's What It Is) 💡

After my third failed attempt to find a proper solution, I remembered a trick we used in a different system years ago. What if I just... fed it an empty file?

My first try was with an empty text file. Crashed spectacularly. Power Automate knew it was being tricked. Cue facepalm moment.

But then I tried a minimal PDF — literally the smallest valid PDF structure possible:

```plaintext
%PDF-1.1
1 0 obj
<< /Type /Catalog /Pages 2 0 R >>
endobj
2 0 obj
<< /Type /Pages /Count 0 >>
endobj
xref
0 3
0000000000 65535 f 
0000000010 00000 n 
0000000053 00000 n 
trailer
<< /Root 1 0 R /Size 3 >>
startxref
87
%%EOF
```

And it WORKED. I'm not saying I did a victory dance around my desk, but my office chair definitely did a full 360° spin.

Is this elegant? Not even close. But hey, sometimes the messiest hacks are the ones that save your deadline.

## How I Actually Implemented This

Here's how I set it up in our production environment (which, by the way, has ridiculous DLP policies that made this whole ordeal even more fun):

1. **Set Up the Logic**
   - Started with our standard manual trigger
   - Added a condition checking if a file exists
   - Tried five different approaches before landing on what actually worked

2. **The Empty PDF Magic**
   - Created a string variable (I named mine "EmptyPDFTrick" so others would know exactly what shenanigans I was up to)
   - Pasted that PDF skeleton above into it
   - Skipped the Compose action because we needed all the performance we could get

3. **The Conditional Logic**
   - When there's a real file: Used that in the custom prompt
   - When there's no file: Fed it my empty PDF imposter
   - Watched in satisfaction as it ran flawlessly. FINALLY.

Here's my actual flow (company identifiers removed to protect the innocent):

![Screenshot of an example flow](/assets/img/posts/flow_example.png)

## The Technical Details (For Those Who Care)

Our trigger is set up with an optional file input, like this:

![Trigger inputs showing file option](/assets/img/posts/trigger_inputs.png)

And the expression I'm using to make this work is:

```
if(equals(null,triggerBody()?['file'])?['contentBytes']),outputs('Compose_empty_pdf'),triggerBody()?['file']?['contentBytes'])
```

When I first wrote this expression, I had to triple-check it because one misplaced parenthesis and the whole thing would explode. Been there, done that, got the error screenshots to prove it.

## Why This Actually Works

It's like bringing an inflatable pool toy to a swimming test. Technically, you're floating, even if it's not how the instructors intended.

The PDF validation only checks if:
1. The file starts with %PDF
2. The structure is technically valid
3. It can be parsed

It doesn't care if there's actual content. It's like the bouncer who only checks if your ID exists, not if it's any good.

## Real Life Scenarios Where This Saved My Bacon

This wasn't just a theoretical exercise. This trick saved our quarterly review process last month when we had to process 178 documents, around 70 of which had no attachments.

Before finding this workaround, I had built TWO SEPARATE FLOWS:
- One for submissions with files
- One for submissions without

Tried maintaining that setup for two weeks. Absolute nightmare. 0/10, would not recommend.

With this trick, we consolidated everything into one flow. The business users have no idea about the digital contortions happening behind the scenes, and that's exactly how it should be.

## Tips From Someone Who Learned The Hard Way

1. **Name Things Clearly**
   I've inherited enough flows with mysterious variable names like "Variable1" to know better. Name it "EmptyPDFForMissingFiles" and save the next person some grief.

2. **Comment Your Flow**
   Trust me on this one. In six months, you'll have NO MEMORY of why this weird PDF thing is in your flow. Future You will thank Present You for the explanation.

3. **Add Error Handling**
   Because something will eventually break. It always does. My flow has a nice try-catch that emails me if things explode so I can fix it before anyone notices.

## Other Approaches I Tried Before Landing Here

Let's save you some time:

1. **Using Flow Branches**
   Tried splitting the flow based on whether a file exists. Ended up with an overly complex mess.

2. **Multiple Custom Prompts**
   Attempted using different prompt actions for with/without file scenarios. The maintenance was a nightmare.

3. **Waiting for Microsoft to Fix It**
   Ha! I still have the support ticket number. No movement in weeks.

Sometimes you just need to solve your own problems.

## Final Thoughts 

This isn't the most beautiful solution I've ever created. In fact, it feels a bit like using duct tape to fix a leaky pipe. But in the world of enterprise automation, sometimes duct tape is all you've got.

And you know what? It's been running flawlessly for three months across four different flows processing hundreds of documents. Sometimes the ugly solutions are the ones that keep working when the elegant ones fall apart.

Have you run into similar limitations? Found your own questionable-but-effective workarounds? Drop them in the comments — always looking to add more tricks to my "break glass in case of emergency" toolkit.

#PowerAutomate #RealWorldAutomation #HacksAndWorkarounds #MicrosoftFlow 