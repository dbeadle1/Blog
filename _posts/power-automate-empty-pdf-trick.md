---
title: "The Empty PDF Trick for Power Automate Custom Prompts"
date: 2024-03-28 15:30:00 -0500
categories: [Tips & Tricks, Power Automate]
tags: [power-automate, automation, microsoft, tips, custom-prompts, workflow, solution]
image:
  path: /assets/img/headers/custom-prompts.jpg
  alt: Power Automate Custom Prompts
---

# The Empty PDF Trick for Power Automate Custom Prompts 🔧

When working with Custom Prompts in Power Automate, you'll likely hit an annoying limitation: every input is treated as mandatory, even if they are optional inputs for your flow. This post walks through a neat little workaround for handling optional file inputs in a platform that just won't take "no file" for an answer.

### What Are Custom Prompts? *(Skip this if you're already using them)*

Custom prompts let you plug GPT-powered AI into your Power Automate flows. They're a way to feed in your own instructions and get useful, structured output—like summaries, rewrites, or extracted insights—based on text or files you provide.

You define what kind of input the AI should expect (like text, a file, or both), and write a prompt that tells it what to do: summarise, analyse, extract info, rewrite, you name it.

They are part of AI Builder and a powerful way to add intelligence to your automations, once you work around the quirks.

More on custom prompts here: [official documentation](https://learn.microsoft.com/en-us/ai-builder/use-a-custom-prompt-in-flow).

## The Challenge: Mandatory File Inputs

Let's say you've got a custom prompt that takes two inputs: a text description and a file to analyze. Sometimes there's no file, and you'd expect the flow to just handle it. But nope, if that file input is empty, you'll get slapped with a bad request error:

![Bad request error in Power Automate](/assets/img/posts/badrequest.png)

You might try passing `null`, an empty string, or even a `.txt` file. No dice. It has to be a PDF.

## The Fix: The Empty PDF Trick 💡

So here's the workaround: give it what it wants. A PDF. A completely empty one.

Here's the world's smallest valid PDF:

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

It ticks all the PDF boxes and adds practically zero overhead to your flow.

## Implementation Steps

Here's how to build this into your flow:

### 1. Set Up Conditional Logic  
Start with your preferred trigger (manual, automated, whatever fits).

### 2. Create the Empty PDF Variable  
Add a Compose step called 'Compose_empty_pdf'. Paste in the PDF code above as its value.

### 3. Conditional File Handling  
Check if a file was provided:

- If yes: use the uploaded file

- If no: use your empty PDF (from either a Compose step or a string variable)

Example expression:

```plaintext
if(
  equals(triggerBody()?['file'], null),
  outputs('Compose_empty_pdf'),
  triggerBody()?['file']?['contentBytes']
)
```

You can use a Compose action to hold the minimal PDF string, or store it in a string variable if you prefer. Either approach works—just keep it consistent and easy to maintain.

![Screenshot of an example flow](/assets/img/posts/flow_example.png)

## Why This Works

Power Automate just wants:  
- A file that starts with `%PDF`  
- Something that looks like a PDF structure

This empty file passes the check. It's tiny, valid, and doesn't interfere with your flow downstream.

## Where This Trick Shines

Some great use cases:

- **Document Review Workflows**: Sometimes you have supporting docs, sometimes you don't  
- **Form Submissions**: Not every form needs an attachment  
- **Approvals**: Requirements vary by case. This lets you keep one flexible flow instead of splitting things up

You save yourself the pain of managing multiple flows or complex branching.

## Best Practices

- **Name things clearly**: Call your variable something obvious like `EmptyPDFForMissingFiles`  
- **Comment your steps**: Future you (or a teammate) will thank you  
- **Handle errors gracefully**: Wrap your custom prompt in error handling for when something does go wrong

## What Didn't Work

Other things we tried:

- **Separate Flows**: Adds maintenance overhead  
- **Multiple Custom Prompts**: More actions = more complexity  
- **Other file types**: Doesn't fly. Power Automate still wants a PDF

## Final Thoughts

This isn't a glamorous solution, but it works. The empty PDF trick helps you sidestep one of the more rigid aspects of Power Automate's custom prompts. When the system demands a file, give it a fake one and move on with your life.

Got your own workaround or clever fix? Drop it in the comments.

#PowerAutomate #Automation #WorkflowSolutions #MicrosoftFlow #TechnicalTips 