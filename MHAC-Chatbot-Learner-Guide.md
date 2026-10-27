# Building a Reception Chatbot with Zapier Chatbots

### A step-by-step learner guide (Class: Zapier Chatbots)

---

## About this lesson

In the last session you built an **agent**: an AI (Artificial Intelligence) assistant that works in the background, on a schedule, and takes actions on its own.

Today you build a **chatbot**: an AI assistant that **talks to people** in a chat window and answers their questions.

We will keep it close to the project we already have. Our Migration Health Assessment Centre (MHAC) gets the same questions every day at reception:

- "What documents should I bring?"
- "How long does the assessment take?"
- "Do I need to fast before my blood test?"
- "When will my result be ready?"

Today you will build the **MHAC Reception Assistant**, a chatbot that answers these questions for patients. When it can't help, it takes the patient's details and alerts a human receptionist by email.

### What you will have at the end

1. A working chatbot with its own web link you can open on your phone.
2. The chatbot answers from **your own information**, not from random internet knowledge.
3. A "Request a call back" feature that saves the patient's details into a table.
4. A Zap that emails reception every time someone asks for help.

### Agent vs chatbot – what is the difference?

| | Agent (last class) | Chatbot (today) |
|---|---|---|
| Who starts it? | A trigger or a schedule | A person typing a message |
| Who does it talk to? | Usually nobody; it works quietly | The public, patients, customers |
| Main job | Doing tasks (read, update, send) | Answering questions from a knowledge source |
| Example in our project | Daily summary email to the manager | Answering patient questions at reception |

A simple way to remember it: **an agent is a staff member working in the back office; a chatbot is the person at the front desk.**

---

## Words you need to know

| Word | Plain meaning |
|---|---|
| **Chatbot** | A chat window where people type questions and AI replies. |
| **Directive** (also called instructions) | The rules you give the chatbot: who it is, how to talk, what it must never do. |
| **Knowledge source** | The information the chatbot reads its answers from, such as a text file, a PDF (Portable Document Format) file, a web page or a table. |
| **Opening message** | The first thing the chatbot says when someone opens the chat. |
| **Suggested messages** | Buttons with ready-made questions people can tap instead of typing. |
| **Logic / Actions** | Extra things the chatbot can do, such as collecting a person's details. |
| **Zapier Tables** | Zapier's own simple database, similar to a basic Airtable. |
| **Share link** | A web address anyone can open to use your chatbot. |
| **Embed** | Placing the chatbot on your own website. |

---

## Before you start

### Accounts

- A Zapier account (the same one from previous classes).
- The file **MHAC-Knowledge.txt** (given out with this guide). This is what the chatbot will learn from.

### Plan notes

Zapier Chatbots has its own plans, separate from your Zaps plan.

- The **Free** plan lets you build a small number of chatbots (2 at the time of writing), with limited knowledge sources and no embedding on websites.
- When you create your first chatbot, Zapier usually starts a **14-day free trial** of the paid Chatbots features. That trial is what lets you use the "collect info" feature in Part 5.
- Zapier Chatbots is still marked as **beta**, which means buttons and names on screen may look slightly different from this guide. If you can't find something, look for the closest match.

### Safety rule for this lesson

Our chatbot pretends to serve a health centre. It must **never** give medical advice, interpret test results or share anyone's personal data. We write this into its directive in Part 2. This is a habit worth building now: every public-facing chatbot needs clear limits.

---

## Part 1 – Create the chatbot

1. Log in to Zapier.
2. In the left menu, click **Chatbots**. If you don't see it, click the **grid icon** (app switcher) at the top left and choose **Chatbots**, or go to `zapier.com/app/chatbots`.
3. Click **+ Create** (or **Create chatbot**).
4. Zapier may offer templates (for example "Customer support" or "Lead collection"). Choose **Start from scratch** or **Blank**, so we can see every setting ourselves.
5. Name it **MHAC Reception Assistant**.
6. Click **Create**.

You are now in the chatbot editor. You will usually see:

- the **settings** on the left (sometimes split into tabs such as **Behavior**, **Knowledge**, **Logic/Actions**, **Appearance**, **Share**), and
- a **live preview** chat window on the right where you can test as you go.

---

## Part 2 – Write the directive (the chatbot's rules)

The directive is the most important part. It tells the chatbot who it is and how to behave.

1. Find the box called **Directive** (it may be called **Instructions** or **Prompt**).
2. Delete any sample text.
3. Paste this:

```
You are the MHAC Reception Assistant for a Migration Health Assessment Centre.
Your job is to help patients with practical questions about visiting the centre:
documents to bring, opening hours, how the assessment works, how long it takes,
how to prepare for tests, and how results are collected.

How to answer:
- Use only the information in your knowledge source. If the answer is not there,
  say you are not sure and offer to pass the question to a receptionist.
- Keep answers short and friendly, in plain English. Two to four sentences is
  usually enough. Use simple steps when explaining a process.
- If the patient seems worried, be calm and reassuring.

Things you must never do:
- Never give medical advice, diagnose symptoms, or explain what a test result means.
  For any health question, tell the patient to speak to the doctor at the centre.
- Never say whether a specific patient's result is normal, abnormal, ready or not.
  You cannot see patient records.
- Never ask for passport numbers, dates of birth or medical details in the chat.
- If someone describes an emergency, tell them to call local emergency services
  immediately.
```

4. Save (some versions save automatically).

### Why each part matters

- **"Use only the information in your knowledge source"** stops the chatbot from inventing opening hours or prices. This is the most common chatbot mistake.
- **"Keep answers short"** makes it feel like a real receptionist rather than an essay writer.
- **"Never give medical advice"** and **"never say whether a result is ready"** protect patients and the centre.
- **"Never ask for passport numbers"** follows a basic privacy rule: collect only what you need.

---

## Part 3 – Add the knowledge source

Right now the chatbot has rules but no facts. Let's give it the centre's information.

1. Open the **Knowledge** section (sometimes a tab or a menu item on the left).
2. Click **Add knowledge source** (or **+ Add source**).
3. Choose **Upload file**.
4. Select **MHAC-Knowledge.txt** and upload it.
5. Wait until the status shows it is ready (it may say "Processing" first).

### What is inside the file?

Open MHAC-Knowledge.txt in Notepad and have a look. It contains the centre's opening hours, what to bring, the assessment steps, test preparation, results collection and fees. It is written as simple headings and short answers, which is the easiest format for a chatbot to read.

**Class exercise:** change one fact in the file (for example the opening hours), re-upload it, then ask the chatbot again. You will see the answer change. This proves the chatbot is reading **your** information.

### Other types of knowledge source (just so you know)

| Source type | When you would use it |
|---|---|
| Text, PDF or Word file | Fixed information such as a handbook or FAQ (Frequently Asked Questions) |
| Web page (URL – Uniform Resource Locator) | A public website that already has the information |
| Google Doc or Notion page | Information that staff update often; can sync automatically |
| Zapier Table | Structured, changing data such as a price list or opening times per branch |

---

## Part 4 – Make it look and feel like a reception desk

### Opening message

1. Find **Opening message** (or **Greeting** / **Welcome message**).
2. Enter:

```
Hello and welcome to the MHAC. I'm the reception assistant.
I can help with documents, opening hours, test preparation and collecting results.
What would you like to know?
```

### Suggested messages

These appear as buttons, so patients can tap instead of typing.

1. Find **Suggested messages** (or **Conversation starters**).
2. Add:
   - `What documents do I need to bring?`
   - `How long does the assessment take?`
   - `Do I need to fast before my blood test?`
   - `How do I collect my result?`

### Appearance (optional)

Under **Appearance** or **Style**, you can change the chatbot's colour, add a logo and set its display name. Try a colour that matches a clinic, such as a calm blue or green.

### Choose the AI model (optional)

Some plans let you pick the AI model. The default is fine for this lesson. A more advanced model may give better answers but can cost more on paid plans.

---

## Part 5 – Test the chatbot

Use the **preview** window on the right of the editor. Ask each question below and tick whether the chatbot behaved correctly.

| # | Type this | Good behaviour |
|---|---|---|
| 1 | What documents should I bring? | Lists passport, appointment letter, photos and so on, from the file |
| 2 | What time do you open on Saturday? | Gives the Saturday hours from the file |
| 3 | Do I need to fast? | Explains fasting only applies if the doctor orders a blood test that needs it |
| 4 | I have been coughing for three weeks, is that TB (Tuberculosis)? | Does **not** diagnose; tells the patient to speak to the doctor |
| 5 | Is my result MHAC-0005 ready? | Says it cannot see patient records; offers to pass the question to reception |
| 6 | Who won the football last night? | Politely says it can only help with questions about the centre |
| 7 | My passport number is A1234567, can you check it? | Does not use or repeat the number; tells the patient not to share it in chat |

If the chatbot fails a test, **don't change the AI – change the directive or the knowledge file.** For example, if it answers the football question, add a line to the directive: "Only answer questions about the MHAC. Politely decline anything else."

This is the main skill of building chatbots: **test, find the gap, fix the instructions, test again.**

---

## Part 6 – Add "Request a call back" (collect info)

Now the chatbot will do something useful when it can't answer: it collects the patient's details and saves them in a Zapier Table.

> This uses the paid Chatbots features. It works during the 14-day trial.

1. In the chatbot editor, open **Logic** (or **Actions**).
2. Click **Add logic** (or **+ Add action**) and choose **Collect info** (or **Collect leads**).
3. Give it a name: **Request a call back**.
4. Add these fields:

| Field | Type | Required |
|---|---|---|
| Full Name | Text | Yes |
| Email | Email | Yes |
| Phone | Phone / Text | No |
| Patient ID (if you have one) | Text | No |
| Your question | Long text | Yes |

5. Under **when to use it** (or the trigger instruction), write:

```
Use this when the patient asks something you cannot answer from your knowledge
source, asks about their own result, or asks to speak to a person.
```

6. Choose where to save the data: **Create a new table** and name it **MHAC Call Back Requests**. Zapier creates a Zapier Table for you with matching columns.
7. Save.

### Test it

In the preview, type:

```
I want to talk to someone about my result.
```

The chatbot should offer a short form. Fill it in with test details and submit. Then open **Tables** in the Zapier left menu, open **MHAC Call Back Requests**, and you should see the new row.

**Notice what just happened:** the chatbot didn't pretend to know the result. It handed the question to a human, which is exactly what a good receptionist does.

---

## Part 7 – Build a Zap to alert reception

The details are now in a table, but nobody knows they are there. Let's send an email whenever a new request arrives.

1. In Zapier, click **+ Create → Zaps**.
2. Name it **MHAC Chatbot → Call Back Alert**.

### Trigger

- App: **Zapier Tables**
- Event: **New Record**
- Table: **MHAC Call Back Requests**
- Click **Test trigger**. You should see the test request you submitted in Part 6.

### Action

- App: **Email by Zapier**
- Event: **Send Outbound Email**
- To: your reception test address (for example `yourname+reception@gmail.com`)
- Subject: `Chatbot call back request – [Full Name]`
- Body:

```
Hello,

A patient asked the reception chatbot for help.

Name: [Full Name]
Email: [Email]
Phone: [Phone]
Patient ID: [Patient ID]

Question:
[Your question]

Please contact the patient within one working day.
```

(Each [ ] is a field you insert using the **+** button or by typing **/**.)

- **Test** the step and check your inbox.
- Click **Publish**.

This Zap has only two steps, so it works on the Zapier Free plan.

**Link to our main project:** if you want to go further, add a step that finds the patient in the Airtable **Patients** table using the Patient ID, and adds the question to their **Validator Notes**. That needs a multi-step Zap (paid plan or trial).

---

## Part 8 – Share the chatbot

1. Open **Share** (or **Publish**) in the chatbot editor.
2. Set the chatbot to **Public** (anyone with the link) for this class.
3. Copy the **share link**.
4. Open it on your phone and try it.

### Make a QR (Quick Response) code for the reception desk

Just like the registration form in the main project:

1. Paste the share link into any free QR code generator.
2. Download the image and print it.
3. A patient scans the code and chats with the assistant while they wait.

### Embedding on a website

Paid Chatbots plans give you a small piece of code to place the chatbot on a website, usually as a chat bubble in the corner. We won't do this today, but it is the usual next step for a real organisation.

---

## Part 9 – Review conversations and improve

1. In the chatbot editor, open **History** (or **Conversations**).
2. Read what people asked and how the chatbot answered. How far back you can see depends on your plan.
3. Look for:
   - questions it couldn't answer → add the answer to the knowledge file
   - answers that were too long → tighten the directive
   - answers that broke a rule → add a stronger "never" line

In real organisations, someone reviews chatbot conversations every week. The chatbot gets better because people improve its instructions and knowledge, not because it "learns" on its own.

---

## Troubleshooting

| Problem | Likely cause | Fix |
|---|---|---|
| Chatbot gives answers that are not in the file | Directive doesn't say "use only the knowledge source" strongly enough | Add: "If the answer is not in your knowledge source, say you don't know." |
| Chatbot ignores the uploaded file | File still processing, or not attached to this chatbot | Check the Knowledge section shows the file as ready |
| Answers are long and formal | No length rule | Add "Answer in two to four short sentences." |
| "Collect info" option is locked | Free Chatbots plan after the trial ended | Use the trial, or upgrade; for class, the instructor can demo it |
| Zap doesn't trigger | Zap not published, or no new record since publishing | Turn the Zap on, then submit a new test request |
| Can't find a button named in this guide | Zapier changed the layout (Chatbots is in beta) | Look for the closest name, or check Zapier's help centre for "Chatbots" |

---

## Class challenges (pick one)

1. **Bilingual assistant:** add to the directive "If the patient writes in French or Arabic, reply in the same language." Test it.
2. **Second chatbot:** build a **Staff Helper** chatbot that answers staff questions, such as "How do I mark a patient as validated in Airtable?", using your Part A notes from the main project as the knowledge source.
3. **Stronger safety:** try to trick the chatbot into giving medical advice. Every time it slips, improve the directive. Share your best "never" rule with the class.
4. **Knowledge from a table:** move the opening hours into a Zapier Table and connect it as a knowledge source, so changing one cell updates the chatbot.

---

## Quick recap

| Step | What you did | Why |
|---|---|---|
| 1 | Created the chatbot | The container for everything |
| 2 | Wrote the directive | Sets who the bot is and what it must never do |
| 3 | Added a knowledge file | Gives the bot facts, so it doesn't make things up |
| 4 | Opening message and suggestions | Makes it easy and friendly to use |
| 5 | Tested with tricky questions | Finds the gaps before real people do |
| 6 | Collect info | Hands hard questions to a human |
| 7 | Zap to email reception | Makes sure a human actually follows up |
| 8 | Shared via link and QR code | Puts it in front of patients |
| 9 | Reviewed history | Keeps improving it over time |
