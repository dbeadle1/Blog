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

When working with Custom Prompts in Power Automate, you may encounter a frustrating limitation: all inputs are mandatory, even when they logically should be optional. This post shares a practical solution for handling optional file inputs in a workflow environment that doesn't support them natively.

## What Are Custom Prompts?

Custom prompts in Power Automate allow you to integrate GPT models into your automation flows. They're part of AI Builder and provide a way to generate text based on specific instructions.

These AI-powered actions can enhance your flows with intelligent text generation, but they come with some limitations. For more details, see Microsoft's [official documentation](https://learn.microsoft.com/en-us/ai-builder/use-a-custom-prompt-in-flow).

## The Challenge: Mandatory File Inputs

The specific scenario: a custom prompt that requires two inputs—a text description and a file to analyze. The problem arises when there isn't always a file to upload.

Custom prompts treat every configured input as mandatory, with no built-in option to make any input optional. When attempting to run a flow without providing a required file, you'll encounter an error like this:

![Bad request error in Power Automate](/assets/img/posts/badrequest.png)

## The Solution: An Empty PDF File 💡

After exploring several approaches, a simple but effective solution emerged: providing a minimal, valid PDF file when no actual file is available.

Here's the smallest valid PDF structure that works for this purpose:

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

This minimal PDF satisfies the input requirement while adding negligible overhead to your flow.

## Implementation Steps

Here's how to implement this solution in your flow:

1. **Set Up Conditional Logic**
   - Start with your trigger (manual, automated, etc.)

2. **Create the Empty PDF Variable**
   - Add a string variable named "EmptyPDFContent"
   - Set its value to the PDF content shown above

3. **Configure the Conditional Path**
   - When there's a real file: Use the actual file in the custom prompt
   - When there's no file: Use the empty PDF variable instead

Here's an example flow structure:

![Screenshot of an example flow](/assets/img/posts/flow_example.png)

The expression used to implement the conditional logic is:

```
if(equals(null,triggerBody()?['file'])?['contentBytes']),outputs('Compose_empty_pdf'),triggerBody()?['file']?['contentBytes'])
```

This expression checks if the file input is null or empty and provides the appropriate content accordingly.

## Technical Implementation

The trigger can be set up with an optional file input:

![Trigger inputs showing file option](/assets/img/posts/trigger_inputs.png)

## Why This Works

This solution works because:

1. The PDF validation only verifies:
   - The file starts with %PDF
   - The structure is technically valid
   - It can be parsed

2. The empty PDF satisfies these requirements while:
   - Being extremely small (less than 1KB)
   - Having no actual content to process
   - Not affecting downstream operations

## Practical Applications

This technique is particularly useful for:

1. **Document Review Processes**
   - When supporting documents are optional but the workflow is consistent

2. **Form Submissions**
   - For handling forms where attachments may or may not be provided

3. **Approval Workflows**
   - When documentation requirements vary by case type

Using this approach allows consolidation of multiple flows into a single flow, significantly reducing maintenance overhead.

## Best Practices

For maintainability and clarity:

1. **Use Descriptive Names**
   - Name your variable something clear like "EmptyPDFForMissingFiles"

2. **Add Comments**
   - Document the purpose of the empty PDF in your flow

3. **Implement Error Handling**
   - Add appropriate error handling around your custom prompt actions

## Alternative Approaches Considered

Other potential solutions, each with their own drawbacks:

1. **Creating Separate Flows**
   - One for submissions with files, one without
   - Increases maintenance complexity significantly

2. **Multiple Custom Prompts**
   - Different actions for different scenarios
   - Leads to workflow duplication

3. **Different File Types**
   - Empty text files or other formats
   - May not work with all custom prompt configurations

## Conclusion

While not the most elegant solution, this empty PDF approach provides a practical workaround to a current limitation in Power Automate custom prompts. It enables streamlined workflows when dealing with optional file inputs in a system that doesn't natively support them.

Have you encountered similar limitations in Power Automate? Share your own solutions in the comments.

#PowerAutomate #Automation #WorkflowSolutions #MicrosoftFlow #TechnicalTips 