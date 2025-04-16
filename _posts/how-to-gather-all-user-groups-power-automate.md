---
title: "How to Gather ALL of a User's Groups in Power Automate"
date: 2024-04-16 16:30:00 -0500
categories: [Tips & Tricks, Power Automate]
tags: [power-automate, microsoft365, azure-ad, groups, microsoft-graph, workflow, automation]
image:
  path: /assets/img/headers/user-groups.jpg
  alt: User Groups in Power Automate
---

# How to Gather ALL of a User's Groups in Power Automate 🔄

Trying to grab all the groups a user belongs to in Microsoft 365? It might seem a bit complicated—especially when those groups are nested or spread across multiple "pages" of results. But don't worry; Power Automate makes it totally doable with just a little bit of setup using a "Do Until" loop. Let's walk through it:

## Why Bother with a Loop? 🤔

When you ask Microsoft Graph for a user's groups, it might not give you everything in one go, especially if the person's in a lot of groups. Think of it as trying to carry all your groceries in one trip—sometimes, you just can't fit everything at once. Microsoft Graph has a limit on how much it can send back in a single response, so it breaks it up into smaller chunks. The "Do Until" loop is like saying, "Okay, keep picking up the next chunk until we've got everything."

## The Steps: Setting Up the Flow

### 1. Identify the User We're Interested In
First, we use the **Get user profile (V2)** action. This just tells Power Automate who we're talking about and gets the user's ID. Easy!

![Get User Profile Action](/assets/img/posts/get-user-profile.jpg)

### 2. Prepare a Place to Collect the Groups
Next, we create an array variable called `Usergroups`. Think of this as our shopping cart where we'll drop all the group IDs we find along the way. We also set up another variable called `varNextlink`. This is where we store the link to the next "page" of results (if there is one). Initially, we give this variable a value to kick off our very first request to Microsoft Graph and start gathering groups:

```plaintext
https://graph.microsoft.com/v1.0/users/@{outputs('Get_user_profile_(V2)')?['body/id']}/transitiveMemberOf
```

### 3. Let's Get Looping: The "Do Until" Loop
The "Do Until" loop is where the magic happens. It keeps running until there's no more data to fetch. How does it know when to stop? Simple—it checks if `varNextlink` is empty:

```plaintext
@equals(empty(variables('varNextlink')),true)
```

If it's empty, the loop knows it's time to wrap things up. Otherwise, it keeps going!

![Do Until Loop Configuration](/assets/img/posts/do-until-loop.jpg)

Inside the Do Until loop, we:
- Send an HTTP Request: This uses the current value of `varNextlink` to ask for the next set of groups. If Microsoft Graph has more for us, it sends back another link for the next batch.
- Select Group IDs: From the response, we only grab the group IDs. No need to overcomplicate things—just the essentials.

### 4. Combining What We've Found So Far
Once we have those group IDs, we use a function called `union()` to smoosh everything together in one neat list. It makes sure that any duplicate groups are removed, so we don't accidentally count the same group twice. This keeps our list tidy and accurate.

Then, we update our `Usergroups` variable to hold this new, combined list.

![Combining Groups](/assets/img/posts/combining-groups.jpg)

### 5. Check if There's More to Fetch
Also inside our Do Until loop, we update `varNextlink` with the next page link (if there is one). If there's no next page, this link is left blank, and the loop knows to stop. That's it! The loop continues to run until everything's been grabbed.

```plaintext
@{body('Send_an_HTTP_request')?['@odata.nextLink']}
```

## And That's a Wrap! 🎉

Once the "Do Until" loop finishes, the `Usergroups` variable contains all the groups the user is a part of—no matter how many pages of results it took to get there. This flow does all the hard work for you, and you end up with the complete list of groups, all in one go.

So, whenever you need to round up all of a user's groups, you've got this simple pattern to follow. It's efficient, flexible, and works like a charm!

#PowerAutomate #Microsoft365 #AzureAD #Automation #MicrosoftGraph #WorkflowTips 