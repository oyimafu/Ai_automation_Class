# Building a Migration Health Assessment Centre Automation with Airtable and Zapier

### A step-by-step learner guide

---

## About this guide

This guide walks you through building an automation that imitates the daily operations of a Migration Health Assessment Centre (MHAC). A patient registers, staff validate the details, a doctor sees the patient, a lab officer runs tests, and the receptionist hands over the result.

You will use two tools:

- **Airtable** as the database and the forms. Every patient gets a unique ID, and one patient can have many doctor visits and many lab results (a one-to-many relationship).
- **Zapier** to move information between steps and send the emails automatically, with AI (Artificial Intelligence) steps added where they genuinely help.

No prior experience with either tool is assumed. Every step explains what to click and why you are doing it.

> **Important:** This is a training simulation. Use made-up patient data only. Real health records fall under data protection laws such as the UK GDPR (General Data Protection Regulation), and a real clinic would need proper security, consent and access controls that go beyond this exercise.

> **Note on screens:** Airtable and Zapier update their menus often. If a button has a slightly different name on your screen, look for the closest match. The idea behind each step stays the same.

---

## Table of contents

1. How the diagram maps to the tools
2. Words you need to know
3. Accounts and plan limits
4. Part A – Build the Airtable database
5. Part B – Build the forms
6. Part C – Build the Zapier automations (Zaps)
7. Part D – Adding AI agents
8. Part E – Test the whole flow end to end
9. Troubleshooting
10. Ideas for extending the project

---

## 1. How the diagram maps to the tools

| Step in the diagram | What we build |
|---|---|
| Patient visits reception and gets a link or barcode | A QR (Quick Response) code that opens the Airtable registration form |
| Patient fills the form (name, nationality, passport, addresses) | Airtable form on the **Patients** table |
| Entries sent to MHAC Patient Record Database | The **Patients** table in Airtable |
| Email sent to data validators | **Zap 1** – new patient triggers an AI data check and an email to validators |
| Validators check the record, then it goes to the doctor | Validators change the record's stage to "Validated" in Airtable |
| Email sent to doctor; patient asked to see the doctor | **Zap 2** – emails the doctor (with a ready-filled form link) and the patient |
| Doctor fills a form (reason for visit, symptoms, comment, date/time) | Airtable form on the **Consultations** table |
| Data saved to Patient Diagnostics Database | The **Consultations** table |
| Email sent to lab officer with diagnostic info and tests to run | **Zap 3** – AI writes a short lab request, emails the lab officer |
| Lab officer fills form (scan result, comment, date/time) | Airtable form on the **Lab Results** table |
| Result saved to Confidential DB | The **Lab Results** table (kept separate from the other tables) |
| All records combined into a central database | The **Patients** table pulls everything together using linked records and lookup fields |
| Email of result sent to receptionist; patient collects result | **Zap 4** – marks the patient "Results Ready" and emails the receptionist |

Here is the whole flow in one picture:

```
Patient scans QR code
        |
        v
[Airtable form] --> Patients table (Patient ID created automatically)
        |
     ZAP 1: AI checks data quality --> email to Data Validators
        |
Validator reviews in Airtable, sets Stage = "Validated"
        |
     ZAP 2: email to Doctor (with pre-filled form link) + email to Patient
        |
Doctor fills form --> Consultations table (linked to the patient)
        |
     ZAP 3: AI writes lab request --> email to Lab Officer (with pre-filled form link)
        |
Lab Officer fills form --> Lab Results table (linked to patient and consultation)
        |
     ZAP 4: Patient Stage = "Results Ready" --> email to Receptionist
        |
Patient collects result and goes home
```

---

## 2. Words you need to know

### Airtable words

| Word | Plain meaning |
|---|---|
| **Base** | A whole database. Think of it as one Excel workbook. Ours is called "MHAC Operations". |
| **Table** | One sheet inside the base. Ours: Patients, Consultations, Lab Results. |
| **Field** | A column. For example "Full Name" or "Date of Birth". Each field has a type (text, date, single select and so on). |
| **Record** | A row. One patient is one record. |
| **Primary field** | The first column of a table. Airtable uses it as the record's name. We make it the Patient ID. |
| **Linked record field** | A field that connects a record to a record in another table. This is how we build the one-to-many relationship. |
| **Lookup field** | Pulls a value from a linked record, for example showing the doctor's comment on the patient's row. |
| **View** | A saved, filtered way of looking at a table, such as "only patients waiting for validation". |
| **Form** | A web page that adds a new record to a table when someone submits it. |

### Zapier words

| Word | Plain meaning |
|---|---|
| **Zap** | One automation. It has a trigger and one or more actions. |
| **Trigger** | The event that starts the Zap, such as "new record in Airtable". |
| **Action** | What Zapier does after the trigger, such as "send an email". |
| **Task** | Every action that runs successfully counts as one task. Your plan gives you a monthly number of tasks. |
| **Filter** | A check that stops the Zap unless a condition is true. |
| **AI by Zapier** | A step that sends text to an AI model with your instructions and gives back an answer you can use in later steps. |
| **Zapier Agents** | AI assistants that can work on their own, on a schedule, using tools you give them (for example, reading Airtable). |

### How one-to-many works here

```
ONE patient (MHAC-0001)
   |
   +-- MANY consultations (CON-0001, CON-0007 ...)
   |
   +-- MANY lab results    (LAB-0001, LAB-0002, LAB-0009 ...)
```

A patient might come back for a follow-up visit, or the doctor might order three different tests. Instead of adding more and more columns to the patient's row, each visit and each test becomes its own record that points back to the same Patient ID.

---

## 3. Accounts and plan limits

### What you need

1. An **Airtable** account – sign up at airtable.com (free plan is enough for this exercise).
2. A **Zapier** account – sign up at zapier.com.
3. An email address for each "role" so you can watch emails arrive. You can use one inbox with "plus addressing" if you use Gmail, for example:
   - `yourname+validator@gmail.com`
   - `yourname+doctor@gmail.com`
   - `yourname+lab@gmail.com`
   - `yourname+reception@gmail.com`
   - `yourname+patient@gmail.com`

   All of these land in the same inbox, which makes testing easy.

### Zapier plan limits (as of October 2026)

The Zapier Free plan gives 100 tasks a month and allows **two-step Zaps only** (one trigger and one action). Multi-step Zaps and AI by Zapier need a paid plan such as Professional. Free plans also check for new data about every 15 minutes, so emails may arrive with a delay.

Your options:

- **Best for learning the full build:** start the free Professional trial when you sign up. All the Zaps below will work during the trial.
- **Free plan only:** follow the "Free plan version" notes under each Zap. You lose the AI steps and some automatic status updates, but the core flow still works.

Always check zapier.com/pricing for the current limits, since they change.

### Airtable plan limits

The Airtable Free plan has a cap on records per base and on automation runs. This project uses a few hundred records at most, so it is fine. Field-level permissions (hiding columns from certain people) are a paid feature, so in this exercise we keep confidential data in a separate table instead.

---

## Part A – Build the Airtable database

### Step A1. Create the base

1. Log in to Airtable.
2. Click **Create** (or **+ Create a base**) and choose **Start from scratch**.
3. Name the base **MHAC Operations**.
4. Airtable creates a table called "Table 1" with a few sample columns. We will rename and clean it in the next step.

**Why:** the base is the container for all three tables. Keeping them in one base lets them link to each other.

---

### Step A2. Build the Patients table

1. Double-click the tab "Table 1" and rename it **Patients**.
2. Delete the sample columns except the first one (click the small arrow on the column header, choose **Delete field**).
3. Add the fields below. To add a field, click the **+** at the far right of the column headers, type the name, choose the type, then click **Create field**.

| Field name | Field type | Settings and notes |
|---|---|---|
| Auto No | Autonumber | Airtable fills this in itself: 1, 2, 3 and so on. |
| Full Name | Single line text | |
| Email | Email | Not in the original diagram, but we need it to contact the patient. |
| Phone | Phone number | |
| Nationality | Single line text | |
| Date of Birth | Date | Choose a date format such as "European" (DD/MM/YYYY). |
| Sex | Single select | Options: Male, Female |
| Passport Number | Single line text | |
| Residential Address | Long text | |
| Town or City | Single line text | |
| Country | Single line text | |
| Proposed UK Address | Long text | |
| UK Town or City | Single line text | |
| UK Postal Code | Single line text | |
| Stage | Single select | Options in this order: Pending Validation, Needs Correction, Validated, Seen by Doctor, Lab Done, Results Ready, Collected. Set the **default** option to "Pending Validation". |
| Validator Notes | Long text | Validators write what they checked or fixed. |
| AI Data Check | Long text | Zap 1 writes the AI's findings here. |
| Registered On | Created time | Airtable records when the form was submitted. |

#### Make the Patient ID

Now turn the first column into a readable Patient ID.

1. Click the arrow on the first column header (it may be called "Name") and choose **Edit field**.
2. Rename it **Patient ID**.
3. Change the type to **Formula**.
4. Paste this formula:

```
"MHAC-" & REPT("0", 4 - LEN({Auto No} & "")) & {Auto No}
```

5. Click **Save**.

**What the formula does:** it takes the autonumber (for example 7), pads it with zeros to four digits (0007) and puts "MHAC-" in front. So the seventh patient becomes **MHAC-0007**. Because it is based on the autonumber, no two patients can ever get the same ID.

---

### Step A3. Build the Consultations table (Patient Diagnostics Database)

1. Click **+ Add or import** next to the table tabs and choose **Create blank table**. Name it **Consultations**.
2. Clean out the sample columns as before.
3. Add these fields:

| Field name | Field type | Settings and notes |
|---|---|---|
| Auto No | Autonumber | |
| Patient | Link to another record | Choose the **Patients** table. Leave "Allow linking to multiple records" **off**, because each consultation belongs to one patient. When Airtable asks, let it create the matching field in Patients. |
| Reason for Visit | Long text | "What are you here for?" |
| Any Symptoms Lately | Single select | Options: Yes, No |
| Symptom Details | Long text | |
| Doctor's Comment | Long text | |
| Tests Required | Multiple select | Options: Chest X-ray, TB (Tuberculosis) Sputum Test, Blood Test, Urine Test |
| Consultation Date and Time | Date | Turn on **Include time**. |
| Doctor Name | Single line text | |
| Lab Request Summary | Long text | Zap 3 writes the AI summary here. |

4. Rename the first column to **Consultation ID**, make it a **Formula**, and use:

```
"CON-" & REPT("0", 4 - LEN({Auto No} & "")) & {Auto No}
```

**What you just built:** go back to the Patients table. You will see a new column called **Consultations**. That column is the "many" side of the relationship: one patient row can list several consultation IDs.

---

### Step A4. Build the Lab Results table (Confidential Database)

1. Create another blank table called **Lab Results**.
2. Add these fields:

| Field name | Field type | Settings and notes |
|---|---|---|
| Auto No | Autonumber | |
| Patient | Link to another record | Link to **Patients**, single record only. |
| Consultation | Link to another record | Link to **Consultations**, single record only. |
| Test Performed | Single select | Same options as Tests Required. |
| Scan Result | Single select | Options: Normal, Abnormal – Needs Review, Inconclusive – Repeat Test |
| Lab Comment | Long text | |
| Result Date and Time | Date | Turn on **Include time**. |
| Lab Officer Name | Single line text | |
| Result Attachment | Attachment | Optional: an image or PDF (Portable Document Format) of the scan. |

3. Rename the first column to **Lab Result ID**, make it a **Formula**:

```
"LAB-" & REPT("0", 4 - LEN({Auto No} & "")) & {Auto No}
```

**Why a separate table for confidential results:** in the diagram, results go to a "Confidential DB". Keeping results in their own table means you can later share the Patients and Consultations tables with reception staff without exposing results. On paid Airtable plans you can lock tables down further with permissions or Interfaces.

---

### Step A5. Make the Patients table your "Central Database"

The diagram shows all records being merged into a central database. In Airtable you do not need to copy data into a fourth table. Linked records already connect everything, so you add **lookup fields** to the Patients table to see the full picture on one row.

In the Patients table, add these fields:

| Field name | Field type | Settings |
|---|---|---|
| Latest Doctor's Comment | Lookup | Linked field: Consultations. Field to look up: Doctor's Comment. |
| Tests Ordered | Lookup | Linked field: Consultations. Field: Tests Required. |
| Lab Results | (already exists) | Airtable created this when you linked Lab Results to Patients. |
| Scan Results | Lookup | Linked field: Lab Results. Field: Scan Result. |
| Number of Visits | Count | Linked field: Consultations. |

**Why this is better than copying:** if a lab officer corrects a result, the Patients table shows the correction straight away. Copies go out of date; links do not.

---

### Step A6. Create helper fields for pre-filled form links

Later, Zapier will email the doctor a form link that already has the patient filled in, so the doctor never has to search for or type the Patient ID. We store that link in a formula field. You will finish this step after building the forms in Part B, because you need the form's address first. For now, just know that two fields are coming:

- **Doctor Form Link** (in Patients)
- **Lab Form Link** (in Consultations)

---

### Step A7. Create views for each team

Views are filtered lists, and Zapier can watch a view. When a record **enters** a view, Zapier triggers.

In the **Patients** table:

1. Click **Grid view** at the top left, then **+ Create… → Grid**. Name it **1. Pending Validation**.
2. Click **Filter → Add condition**: `Stage` `is` `Pending Validation`.
3. Create another grid view called **2. Ready for Doctor** with filter `Stage` `is` `Validated`.
4. Create **3. Needs Correction** with filter `Stage` `is` `Needs Correction`.
5. Create **4. Results Ready** with filter `Stage` `is` `Results Ready`.

**Why:** each team opens only the view that matters to them, and each view doubles as a trigger point for a Zap.

---

## Part B – Build the forms

### Step B1. Patient registration form

1. Open the **Patients** table.
2. Click the views menu and choose **+ Create… → Form**. Name it **Patient Registration**.
3. Airtable adds all fields to the form. Remove the ones patients should not see by dragging them back to the field list on the left (or clicking the eye icon):
   - Remove: Patient ID, Auto No, Stage, Validator Notes, AI Data Check, Registered On, Consultations, Lab Results, and all the lookup and count fields.
4. For each remaining field, click it and turn on **Required** where needed (Full Name, Email, Date of Birth, Passport Number at least).
5. Add a title and short description at the top, for example: "Welcome to the MHAC. Please fill in your details exactly as they appear on your passport."
6. In the form settings, set the message shown after submission, for example: "Thank you. Please take a seat. A member of staff will check your details shortly."
7. Click **Share form** and copy the link.

#### Turn the link into a barcode (QR code)

1. Go to any free QR code generator (search "QR code generator").
2. Paste your form link and download the image.
3. Print it for the "reception desk". Patients scan it with their phone camera and the form opens.

---

### Step B2. Doctor consultation form

1. Open the **Consultations** table.
2. Create a **Form** view called **Doctor Consultation**.
3. Keep these fields on the form: Patient, Reason for Visit, Any Symptoms Lately, Symptom Details, Doctor's Comment, Tests Required, Consultation Date and Time, Doctor Name.
4. Remove: Consultation ID, Auto No, Lab Request Summary, and anything else not listed above.
5. Click **Share form** and copy the link. It looks something like `https://airtable.com/appXXXXXXXX/shrXXXXXXXX`. Paste it somewhere safe; you need it in Step B4.

---

### Step B3. Lab officer form

1. Open the **Lab Results** table.
2. Create a **Form** view called **Lab Result Entry**.
3. Keep: Patient, Consultation, Test Performed, Scan Result, Lab Comment, Result Date and Time, Lab Officer Name, Result Attachment.
4. Copy the share link and keep it for Step B4.

---

### Step B4. Build the pre-filled links (finishing Step A6)

Airtable forms accept extra text at the end of the link to fill fields in advance:

- `?prefill_FieldName=value` fills a field.
- `&hide_FieldName=true` hides that field so it cannot be changed.

For a linked record field, the value is the primary field of the other table, which for us is the Patient ID or Consultation ID. That is exactly why we made the IDs the primary fields.

**In the Patients table**, add a field:

- Name: **Doctor Form Link**
- Type: **Formula**
- Formula (replace the link with your own Doctor Consultation form link):

```
"https://airtable.com/appXXXXXXXX/shrXXXXXXXX?prefill_Patient="
& ENCODE_URL_COMPONENT({Patient ID})
& "&hide_Patient=true"
```

**In the Consultations table**, first add a helper field so we can read the patient's ID as plain text:

- Name: **Patient ID Text**
- Type: **Formula**
- Formula:

```
ARRAYJOIN({Patient})
```

Then add:

- Name: **Lab Form Link**
- Type: **Formula**
- Formula (replace with your own Lab Result Entry form link):

```
"https://airtable.com/appXXXXXXXX/shrYYYYYYYY?prefill_Patient="
& ENCODE_URL_COMPONENT({Patient ID Text})
& "&prefill_Consultation="
& ENCODE_URL_COMPONENT({Consultation ID})
& "&hide_Patient=true&hide_Consultation=true"
```

**Test it now:** add a test patient through the registration form. Copy the value of their Doctor Form Link field and open it in your browser. The form should open with no Patient field visible. Submit it, then check the Consultations table. The new record should already be linked to the right patient.

> If the field name in your table has spaces, write it in the link with `+` instead of spaces, for example `prefill_Doctor+Name=`. This is why we kept the linked field names short and without spaces.

---

## Part C – Build the Zapier automations

### Before your first Zap: connect Airtable

1. Log in to Zapier.
2. Go to **Apps** (left menu) → **Add connection** → search **Airtable**.
3. Sign in to Airtable when asked and give Zapier access to the **MHAC Operations** base.

You only do this once. Every Zap will reuse this connection.

### About sending emails

The guide uses **Email by Zapier → Send Outbound Email**. It is built in, needs no extra account, and is fine for testing. If you prefer emails to come from your own address, use **Gmail → Send Email** or **Microsoft Outlook → Send Email** instead; the fields are almost the same.

### How every Zap is built

1. Click **+ Create → Zaps**.
2. Click the **Trigger** box, choose the app and event, choose the account, set the options, then click **Test trigger**. Zapier pulls a sample record so you can use its data in later steps.
3. Click **+** to add an action, choose the app and event, fill in the fields, then **Test step**.
4. Name the Zap (top left) and click **Publish**.

When filling in action fields, you click into a box and pick data from earlier steps (for example "1. Full Name"). This is called **mapping**.

---

### Zap 1 – New registration: AI check and email to validators

**Purpose:** when a patient submits the form, an AI step checks the record for obvious problems, saves its findings in Airtable, and emails the data validators.

**Name the Zap:** `MHAC 1 – New Patient → AI Check → Validators`

#### Trigger

- App: **Airtable**
- Event: **New Record**
- Base: MHAC Operations
- Table: Patients
- Limit to View: **1. Pending Validation**
- Click **Test trigger** (make sure you submitted at least one test patient first).

#### Action 1 – AI by Zapier (the "Data Check Agent")

- App: **AI by Zapier**
- Event: **Analyze and Return Data** (or the closest prompt-style action on your screen)
- Instructions, paste:

```
You are a data quality assistant for a migration health assessment centre.
Check this patient registration for problems a human validator should look at.
Look for: missing or obviously fake values, a date of birth in the future or
more than 110 years ago, a passport number with unusual characters, a UK postal
code that does not look like a valid UK format, and town or country mismatches.
Do not make medical judgements. Do not change any data.
Reply with a short list of issues, or "No issues found".
```

- Input data: map these from the trigger: Full Name, Nationality, Date of Birth, Sex, Passport Number, Residential Address, Town or City, Country, Proposed UK Address, UK Town or City, UK Postal Code.
- Output field: create one called **issues** (text).
- Test the step and read the result.

**Why this is useful:** validators may check dozens of forms a day. The AI does a first pass so a human knows where to look. The human still makes the decision.

#### Action 2 – Update the Airtable record

- App: **Airtable**
- Event: **Update Record**
- Base: MHAC Operations, Table: Patients
- Record: map **1. ID** (the record ID from the trigger, it starts with "rec")
- AI Data Check: map the **issues** output from Action 1.

#### Action 3 – Email the validators

- App: **Email by Zapier**
- Event: **Send Outbound Email**
- To: your validator address
- Subject: `New registration to validate – [Patient ID] [Full Name]` (map the fields)
- Body:

```
Hello,

A new patient has registered and needs validation.

Patient ID: [Patient ID]
Name: [Full Name]
Nationality: [Nationality]
Passport Number: [Passport Number]

AI data check:
[issues]

Please open the "1. Pending Validation" view in Airtable, correct any errors,
then set Stage to "Validated" or "Needs Correction".

Thank you.
```

Test and **Publish**.

**Free plan version:** keep only the trigger and Action 3 (email). Validators still get notified; they just do the checks without the AI note.

---

### What the validators do (manual step)

1. Open the Patients table, view **1. Pending Validation**.
2. Read the AI Data Check column and the record.
3. Fix small typos directly in the cells.
4. Write what was done in **Validator Notes**.
5. Change **Stage** to:
   - **Validated** if everything is correct, or
   - **Needs Correction** if the patient must provide more information.

The record disappears from "1. Pending Validation" and appears in the matching view, which fires the next Zap.

---

### Zap 2 – Validated: email the doctor and the patient

**Name:** `MHAC 2 – Validated → Doctor + Patient`

#### Trigger

- App: **Airtable**, Event: **New Record**
- Table: Patients, Limit to View: **2. Ready for Doctor**

**Why this works:** Zapier's "New Record" trigger with a view fires when a record appears in that view. Because the view only shows Stage = Validated, the Zap runs the moment a validator approves someone.

#### Action 1 – Email the doctor

- Email by Zapier → Send Outbound Email
- To: doctor address
- Subject: `Patient ready for consultation – [Patient ID]`
- Body:

```
Hello Doctor,

The following patient has been validated and is ready to see you.

Patient ID: [Patient ID]
Name: [Full Name]
Sex: [Sex]
Date of Birth: [Date of Birth]
Nationality: [Nationality]

Validator notes: [Validator Notes]

When the consultation is finished, please record it using this link
(the patient is already filled in):
[Doctor Form Link]
```

#### Action 2 – Email the patient

- Email by Zapier → Send Outbound Email
- To: map **Email** from the trigger
- Subject: `Your details have been confirmed – please see the doctor`
- Body:

```
Dear [Full Name],

Your registration details have been checked and confirmed.
Your Patient ID is [Patient ID]. Please keep it for your records.

Please proceed to the doctor's room when your ID is called.

MHAC Reception
```

Test and Publish.

**Free plan version:** build two separate two-step Zaps with the same trigger: one emails the doctor, the other emails the patient.

#### Optional Zap 2b – Needs Correction

Same pattern, trigger on view **3. Needs Correction**, email the patient asking them to return to reception with the missing information, and include the Validator Notes.

---

### Zap 3 – Doctor finishes: AI lab request and email to lab officer

**Name:** `MHAC 3 – Consultation → Lab Officer`

#### Trigger

- App: **Airtable**, Event: **New Record**
- Table: **Consultations** (no view needed)

#### Action 1 – AI by Zapier (the "Lab Request Agent")

- Event: **Analyze and Return Data**
- Instructions:

```
You help a doctor's office write clear lab requests.
Using the consultation details, write a short, plain request for the lab officer:
which tests to run, and any symptom notes the lab should know.
Use only the information given. Do not add a diagnosis or any medical advice.
Keep it under 80 words.
```

- Input data: Patient ID Text, Reason for Visit, Any Symptoms Lately, Symptom Details, Doctor's Comment, Tests Required.
- Output field: **lab_request** (text).

#### Action 2 – Save the AI summary on the consultation

- Airtable → **Update Record**
- Table: Consultations, Record: map **1. ID**
- Lab Request Summary: map **lab_request**

#### Action 3 – Move the patient to "Seen by Doctor"

- Airtable → **Update Record**
- Table: **Patients**
- Record: map the **Patient** linked field from the trigger. In the trigger data, a linked record field gives you the linked record's ID (starting with "rec"). That is the patient's record ID.
- Stage: **Seen by Doctor**

#### Action 4 – Email the lab officer

- Email by Zapier → Send Outbound Email
- To: lab officer address
- Subject: `Lab request – [Patient ID Text] – [Tests Required]`
- Body:

```
Hello,

Please run the following tests.

Patient ID: [Patient ID Text]
Consultation ID: [Consultation ID]
Tests required: [Tests Required]

Request from the doctor:
[lab_request]

When you have the result, record it here (patient and consultation are filled in):
[Lab Form Link]
```

Test and Publish.

**Free plan version:** keep only the trigger and Action 4, and put **Doctor's Comment** in the email instead of the AI summary. Validators or the doctor can update the patient's Stage by hand.

---

### Zap 4 – Lab result recorded: mark ready and email reception

**Name:** `MHAC 4 – Lab Result → Reception`

#### Trigger

- App: **Airtable**, Event: **New Record**
- Table: **Lab Results**

#### Action 1 – Update the patient

- Airtable → **Update Record**
- Table: Patients
- Record: map the **Patient** field from the trigger (the "rec" ID)
- Stage: **Results Ready**

#### Action 2 – Email the receptionist

- Email by Zapier → Send Outbound Email
- To: reception address
- Subject: `Result ready for collection – [Patient]`
- Body:

```
Hello,

A lab result has been recorded and is ready for collection.

Lab Result ID: [Lab Result ID]
Test performed: [Test Performed]
Result status: [Scan Result]
Recorded by: [Lab Officer Name] on [Result Date and Time]

Please hand the result to the patient and set their Stage to "Collected"
in Airtable.
```

> **Privacy tip:** in a real clinic you would not put the result itself in an email. You would only say "a result is ready" and staff would open the secure system. Try changing the email to remove the Scan Result line and see how the process still works.

Test and Publish.

**Note on multiple tests:** if the doctor orders three tests, the lab officer submits the form three times (one record per test). Zap 4 will run three times. If you want only one email, add a **Filter by Zapier** step before Action 2 that continues only when, for example, `Test Performed` matches the last test in the list, or move to the Results Ready view approach used in Zap 2.

---

### The final manual step

The receptionist opens the view **4. Results Ready**, hands over the result, and sets Stage to **Collected**. The patient goes home. That is the full loop in the diagram.

---

## Part D – Adding AI agents

You have already added two AI steps:

| Where | What the AI does | What the human still does |
|---|---|---|
| Zap 1 – Data Check | Flags likely errors in the registration | Validator decides and approves |
| Zap 3 – Lab Request | Turns the doctor's notes into a short, clear lab request | Doctor writes the notes; lab officer runs the tests |

A good rule for this project: **AI helps with paperwork, humans make the medical and approval decisions.** The prompts above say this directly ("Do not make medical judgements").

### Optional: a Zapier Agent for a daily operations summary

Zapier Agents are AI assistants that can run on a schedule and use apps you connect. Here is a simple, useful one.

1. In Zapier, open **Agents** from the left menu (or go to agents.zapier.com).
2. Click **Create agent**. Name it **MHAC Daily Supervisor**.
3. Add a trigger: **Schedule – every day at 5:00 PM**.
4. Give it tools: **Airtable – Find Records** (on the Patients table) and **Email by Zapier – Send Outbound Email**.
5. Instructions:

```
Every day at 5 PM, look at the Patients table in the MHAC Operations base.
Count how many patients are in each Stage.
List any patient who has been in "Pending Validation" or "Seen by Doctor"
for more than 4 hours, using Registered On to judge.
Email a short summary to the centre manager at [manager email].
Do not include passport numbers, addresses or lab results in the email.
```

6. Test it, then turn it on.

**Why this is a good agent task:** it reads data and reports, but it does not change records or make decisions, so the risk is low. Agent usage is billed separately from Zap tasks on some plans, so check your plan before leaving it on.

---

## Part E – Test the whole flow end to end

Use one fake patient and follow them through every step. Tick each line as you go.

| # | Action | What you should see |
|---|---|---|
| 1 | Scan the QR code and submit the registration form | New row in Patients with ID like MHAC-0001 and Stage "Pending Validation" |
| 2 | Wait up to 15 minutes (free plan polling) | AI Data Check column filled in; validator email arrives |
| 3 | Set Stage to "Validated" | Doctor email (with link) and patient email arrive |
| 4 | Open the doctor link and submit the form | New Consultations row linked to MHAC-0001; patient Stage changes to "Seen by Doctor" |
| 5 | Wait for Zap 3 | Lab officer email arrives with AI summary and lab link |
| 6 | Open the lab link and submit a result | New Lab Results row linked to both patient and consultation |
| 7 | Wait for Zap 4 | Patient Stage changes to "Results Ready"; receptionist email arrives |
| 8 | Set Stage to "Collected" | Patient row now shows consultation, tests ordered and scan result all in one place |

Now register a second test patient and send them through twice (two consultations). Open their Patients row. You should see two consultation IDs in one cell. That is your one-to-many relationship working.

To see what each Zap did, go to **Zap History** in Zapier. Every run is listed with the data that passed through each step, which is the best place to look when something goes wrong.

---

## 9. Troubleshooting

| Problem | Likely cause | Fix |
|---|---|---|
| Zap does not run at all | Zap not published, or still waiting for the polling interval | Check the Zap is **On**; wait 15 minutes on the free plan, or click **Run** in the Zap editor to check now |
| Zap 2 did not fire a second time for the same patient | The view trigger fires only the first time a record enters the view | Expected behaviour. For corrections, have the validator fix the data and set Validated once |
| Pre-filled form shows an empty patient | Field name in the link does not match exactly, or the Patient ID has spaces | Check spelling and capital letters of `prefill_Patient`; keep linked field names without spaces |
| "Record not found" in an Update Record step | Wrong ID mapped | Map the field that starts with "rec", not the MHAC-0001 text |
| AI step gives long or rambling answers | Prompt too loose | Add a word limit and say exactly what format you want |
| Free plan says multi-step Zaps not allowed | Free plan only allows one trigger and one action | Start the Professional trial, or split into two-step Zaps as described |
| Email lands in spam | Email by Zapier sends from a Zapier address | Use Gmail or Outlook as the action app instead |
| Lookup fields in Patients show nothing | Linked record not filled on the form | Check the form link includes the prefill, and that the record shows a linked patient |

---

## 10. Ideas for extending the project

1. **Interfaces:** build an Airtable Interface (the **Interfaces** tab) with one page per role, so the doctor sees only patients ready for them and never opens the raw tables.
2. **SMS (Short Message Service) alerts:** swap the patient email in Zap 2 for an SMS step using a texting app in Zapier.
3. **Paths:** in Zap 4, use **Paths by Zapier** to send "Abnormal – Needs Review" results to the doctor instead of reception.
4. **PDF report:** add a document step that creates a printable result letter for the patient.
5. **Same build in n8n:** rebuild these four Zaps in n8n and compare how triggers, AI steps and pricing differ.

---

## Quick reference: the four Zaps

| Zap | Trigger | Actions |
|---|---|---|
| 1 | New record in Patients, view "1. Pending Validation" | AI data check → update record → email validators |
| 2 | New record in Patients, view "2. Ready for Doctor" | Email doctor with pre-filled link → email patient |
| 3 | New record in Consultations | AI lab request → update consultation → set patient "Seen by Doctor" → email lab officer |
| 4 | New record in Lab Results | Set patient "Results Ready" → email receptionist |
