---
title: "The Empty PDF Hack That Saved My Power Automate Sanity"
date: 2024-03-28 15:30:00 -0500
categories: [Tips & Tricks, Power Automate]
tags: [power-automate, automation, microsoft, tips, custom-prompts, workflow, life-hacks]
image:
  path: /assets/img/headers/custom-prompts.jpg
  alt: Power Automate Custom Prompts
---

# The Empty PDF Hack That Saved My Power Automate Sanity 🧠💥

Okay, let's talk about that moment when you want to throw your laptop out the window because Power Automate is being... let's say "stubborn." We've ALL been there. 

Picture this: It's 4:45 PM on a Friday. Everyone's mentally checked out for the weekend. Everyone except you, because you're *this close* to finishing a flow that's going to save your team HOURS of work every week. 

Then it happens. 😱

## The Custom Prompt That Ruined My Weekend 🤦‍♀️

So there I was, building what seemed like a simple flow using Custom Prompts. The prompt required a file attachment, but here's the twist – sometimes my users have a file to attach, sometimes they don't.

Should be simple, right? WRONG.

Custom Prompts had other ideas:
- "File input? That's mandatory."
- "Optional file? Never heard of her."
- "No file today? Sorry, I'm going to sit here and sulk until you give me one."

I literally spent two hours trying everything. I may have shouted at my monitor. I definitely stress-ate an entire bag of chocolate. And then I stumbled onto... THE SOLUTION.

## The Magic Empty PDF Hack (That Nobody Tells You About) 💫

I'm about to share the kind of workaround that separates the Power Automate amateurs from the pros. The kind that makes your colleagues go "Wait, how did you DO that?"

Here's the trick: Feed your Custom Prompt a tiny, empty PDF file when you don't have a real file to attach. It's like giving a toddler an empty juice box just to stop the tantrum. 🧃

### The Secret Code (Feel Free to Steal It)

Just add a String Variable to your flow and paste in this magical incantation:

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

I call it my "nothing burger with a side of PDF." It's smaller than this paragraph, but Power Automate sees it as a valid file and stops complaining. GAME. CHANGER.

### How I Actually Set This Up (Real Talk)

Here's how I implemented this in my flow:

1. **When Your Flow Starts**
   - I start with a normal trigger (button click, email received, whatever)
   - Then I add a condition: "Do I actually have a file to attach here?"

2. **The Empty PDF Magic**
   - I create a string variable called "NothingBurger" (because I'm hilarious)
   - I paste that PDF code above into it
   - No Compose step needed – we're keeping it lean!

3. **The Payoff**
   - When I have a real file: I use that in my Custom Prompt
   - When I don't: I feed it my Nothing Burger
   - My flow runs smoothly EITHER WAY

The first time it worked, I did a literal happy dance at my desk. My coworkers now think I'm slightly unhinged, but WORTH IT.

## A Real Implementation Example 📱

Here's exactly how this looks in a real Power Automate flow:

1. **Start with a trigger** (like a manual button)
2. **Add a Compose step** named "Compose empty pdf" with our empty PDF content
3. **Add your Custom Prompt** (like "Create text with GPT")
4. **Use this expression** in the file input to handle both cases:

```
if(equals(null,triggerBody()?['file'])?['contentBytes']),outputs('Compose_empty_pdf'),triggerBody()?['file']?['contentBytes'])
```

This says:
- If the file input is null/empty → use our empty PDF from the Compose step
- Otherwise → use the actual file that was provided

![Screenshot of an example flow](/assets/img/posts/image%20(10).png)

## Why This Actually Works (The Nerdy Explanation) 🤓

This trick works because:
- It's technically a valid PDF file (just empty)
- It's tiny – like 300 bytes tiny
- Power Automate checks "Is this a file?" not "Is this file useful?"
- It satisfies the requirement without causing issues downstream

It's like bringing a pet rock to "Bring Your Pet to Work Day." Technically compliant! 🪨

## Real-Life Scenarios Where This Saved My Bacon 🥓

1. **Our Document Review Process**
   I built a flow where managers review employee submissions. Sometimes they have documentation, sometimes they don't. Before this hack, I had to build TWO SEPARATE FLOWS. Now? One flow handles everything.

2. **The Invoice Approval Nightmare**
   Some invoices need supporting documents, some don't. Our finance team kept rejecting flows with missing attachments. This trick? Problem solved. Finance team mystified but happy.

3. **The HR Forms Process**
   Some employee requests need documentation, some don't need any. The empty PDF trick lets us use the same flow for everything. Our HR team thinks I'm a wizard now.

## My Pro Tips (From Many Painful Lessons) 💪

1. **Name Your Nothing Burger Clearly**
   I like "EmptyPDFForMandatoryInputs" – boring but clear. My colleagues know exactly what it does when they inherit my flows.

2. **Add Comments!**
   Trust me, Future You will have NO IDEA what this weird PDF code does in 6 months. Leave a note explaining the hack.

3. **Create a Flow Template**
   I created a template with this trick already built in. One-click solution that I reuse constantly.

## When Things Go Wrong (Because They Will) 🔧

1. **The Custom Prompt Rejects Your Nothing Burger**
   Double-check that you copied the PDF code exactly. One missing character and it's not a valid PDF anymore.

2. **Your Flow Seems Slower**
   This almost never happens, but if it does, check if you're using the variable directly. Don't route your empty PDF through unnecessary steps.

## Other Tricks I Tried Before Finding This One 🤔

Let me save you some time:

1. **Creating Multiple Flows**
   I tried having separate flows for "with file" and "without file" scenarios. Maintenance nightmare. 0/10 do not recommend.

2. **Using Empty Text Files**
   This works sometimes, but some actions specifically want certain file types. The PDF trick is more universal.

## The Moral of the Story 🎯

Sometimes the most elegant solutions are just clever hacks. Power Automate isn't perfect, but with tricks like this, you can make it bend to your will.

I went from wanting to throw my computer out the window to feeling like a Power Automate superhero. And now you can too! 🦸‍♀️

> "Work smarter, not harder. And when that fails, find a good hack." – Me, after my third coffee

## Your Turn!

Have you used this trick in your flows? Got any other Power Automate hacks that saved your sanity? Drop them in the comments – I'm ALWAYS looking for new tricks to add to my arsenal!

And if this hack saved your Friday afternoon, consider sharing this post. Your fellow Power Automate warriors will thank you! 🙏

#PowerAutomateHacks #WorkflowWizardry #TechTricks #AutomationSanity #MicrosoftTips 