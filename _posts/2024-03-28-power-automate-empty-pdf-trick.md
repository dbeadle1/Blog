---
title: "The Empty PDF Trick for Power Automate Custom Prompts"
date: 2024-03-28 15:30:00 -0500
categories: [Tips & Tricks, Power Automate]
tags: [power-automate, automation, microsoft, tips, custom-prompts, workflow, life-hacks]
image:
  path: /assets/img/headers/custom-prompts.jpg
  alt: Power Automate Custom Prompts
---

# The Empty PDF Trick for Power Automate Custom Prompts 🔧

We've all been there - you're building a flow in Power Automate that's working perfectly until you hit a frustrating roadblock. For me, it was a Friday afternoon when I discovered a particularly annoying limitation with Custom Prompts.

## What are Custom Prompts in Power Automate?

If you're new to this feature, custom prompts in Power Automate allow you to use AI to generate text within your automated processes. They're part of AI Builder and powered by Azure OpenAI's GPT models (currently GPT 4o Mini and GPT 4o).

Custom prompts can be added as an action in your flows, allowing you to:
- Generate text based on specific instructions
- Process input data dynamically
- Create AI-powered responses as part of your automation

You can either use existing prompts or create new ones directly in your flow. The output (generated text) can then be used in subsequent actions like sending messages, creating documents, or any other step in your workflow.

[Learn more about custom prompts in Power Automate](https://learn.microsoft.com/en-us/ai-builder/use-a-custom-prompt-in-flow)

## The Challenge with Custom Prompts 🤔

Here's the specific scenario: I have a custom prompt that accepts two dynamic inputs - a text input and a file. Sometimes I'll be including a file and sometimes I won't. And therein lies the problem.

When working with Custom Prompts in Power Automate, I encountered this common scenario where my prompt required a file attachment, but in some cases, my users wouldn't have a file to attach. The issue is that custom prompts don't allow for optional inputs.

Custom Prompts don't handle this well:
- All input fields are mandatory
- You can't make a file input optional
- The flow won't proceed without all inputs being provided

When you try to run a flow without providing a required file, you'll get an error like this:

![Bad request error in Power Automate](/assets/img/posts/badrequest.png)

## A Simple Solution: The Empty PDF Trick 💡

After some trial and error, I found a practical workaround: use a minimal, empty PDF file when you don't have a real file to attach. This satisfies the mandatory requirement without adding any meaningful overhead.

### The Solution Code

Here's the minimal PDF content you can use:

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

It's just a few lines, but Power Automate recognizes it as a valid PDF file. The file is tiny (less than 1KB) but completely valid.

### Implementation Steps

Here's how to implement this solution:

1. **Set Up Your Flow**
   - Start with your trigger (button, form submission, etc.)
   - Add a condition to check if you have an actual file

2. **Create a String Variable**
   - Name it something clear like "EmptyPDFContent"
   - Set its value to the PDF content above
   - This is more efficient than using a Compose action

3. **Configure Your Custom Prompt**
   - In the "Yes" branch: Use your actual file
   - In the "No" branch: Use the empty PDF string variable

Here's what the flow looks like:

![Screenshot of an example flow](/assets/img/posts/flow_example.png)

## A Practical Example

In this example, I'm using a manual trigger that has an optional file input:

![Trigger inputs showing file option](/assets/img/posts/trigger_inputs.png)

The expression used in the file input for the Custom Prompt is:

```
if(equals(null,triggerBody()?['file'])?['contentBytes']),outputs('Compose_empty_pdf'),triggerBody()?['file']?['contentBytes'])
```

This expression:
- Checks if the file input is null/empty
- If it is, uses our empty PDF from the Compose step
- Otherwise, uses the actual file that was provided

## Why This Works

This solution is effective because:
- It provides a valid file structure that passes validation
- It has minimal size (a few hundred bytes)
- It satisfies the requirement without causing issues
- It doesn't impact performance

## Real-World Applications

This technique has proven useful in several scenarios:

1. **Document Review Processes**
   When building approval flows where supporting documents are optional

2. **Form Submissions**
   For handling forms where file attachments aren't always needed

3. **Approval Workflows**
   When some approvals require documentation and others don't

## Best Practices

If you implement this solution, consider these tips:

1. **Clear Naming**
   Name your variable descriptively (e.g., "EmptyPDFContent")

2. **Add Comments**
   Document what you're doing so others (or future you) understand the purpose

3. **Error Handling**
   Include appropriate error handling around your custom prompts

## Alternative Approaches

While this solution works well, there are alternatives worth considering:

1. **Separate Flows**
   Creating different flows for scenarios with and without files

2. **Different File Types**
   Using empty text files or other formats if your prompt accepts them

## Conclusion

This empty PDF technique is a simple solution to what can be a frustrating limitation in Power Automate custom prompts. It's not the most elegant approach, but it's practical and effective for handling optional file inputs.

Have you found other ways to handle this scenario in your flows? Share your experiences in the comments.

#PowerAutomate #Automation #MicrosoftFlow #ProductivityTips #WorkflowAutomation 