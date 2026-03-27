---
name: invoice-generator
description: "Generate professional invoices for freelancers and contractors. Creates a polished HTML invoice with live preview and download button, saved to /invoices. Stores contractor info (company name, address, phone) in a config file so it's only entered once. Asks for client name, email, invoice number, line items, and optional payment terms each time. The user can customize the HTML template to match their brand. Use this skill whenever the user mentions invoice, billing, bill a client, send an invoice, create an invoice, contractor invoice, freelance billing, or wants to generate any kind of invoice document — even if they just say something like 'I need to bill my client' or 'charge for last month's work'."
---

# Invoice Generator

Generate clean, professional invoices as self-contained HTML files with a live preview and a download-as-PDF button. Invoices are saved to a `/invoices` folder in the current working directory.

## How It Works

The skill uses two pieces:

1. **A config file** (`invoice-generator.local.md` in the project's `.claude/` directory) that stores your company/contractor info so you don't have to re-enter it every time.
2. **An HTML template** (`assets/invoice-template.html` bundled with this skill) that defines the look of the invoice. The user can swap this out or edit it to match their brand.

Each time the skill runs, it reads the config, asks for the per-invoice details (client, items, amounts), fills in the template, and writes the final HTML file.

---

## Step 0: Read the Config

Look for a config file at `.claude/invoice-generator.local.md` in the current project directory. This file uses YAML frontmatter to store the contractor's reusable info.

**If the file exists**, read it and extract the frontmatter fields. Confirm with the user: "I'll use your saved info — [Company Name], [Contractor Name]. Sound good?"

**If the file doesn't exist**, tell the user you need to set up their info once, then ask for:

- `company_name` — Business or company name (appears at the top of the invoice)
- `contractor_name` — The person sending the invoice
- `street_address` — Street address
- `city_state_zip` — City, State ZIP
- `phone` — Phone number
- `fax` — Fax number (optional, skip if not provided)
- `email` — Contractor's email address
- `default_currency` — Currency symbol (defaults to `$`)

Then create the file:

```markdown
---
company_name: "Acme Consulting LLC"
contractor_name: "Jane Smith"
street_address: "123 Main St"
city_state_zip: "Austin, TX 78701"
phone: "512-555-0100"
fax: ""
email: "jane@acmeconsulting.com"
default_currency: "$"
---

# Invoice Generator Config

This file stores your contractor/company info for invoice generation.
Edit the YAML fields above to update your details.
```

## Step 1: Gather Invoice Details

Ask the user for the following (all in one go — don't ask one field at a time):

- **Client name** — Who is this invoice for?
- **Client email** — Their email address
- **Invoice number** — e.g., INV-001, 100, etc.
- **Invoice date** — Default to today's date if not specified
- **Line items** — Each item needs: quantity, description, unit price
- **Payment terms** *(optional)* — e.g., "Net 30", "Due on receipt", bank transfer details, payment link

Present the question naturally. For example:

> I've got your company info loaded. Now I need the details for this invoice:
> - Who's the client? (name and email)
> - What invoice number should I use?
> - What are the line items? (quantity, description, unit price for each)
> - Any payment terms you'd like to include? (e.g., "Net 30", bank details — or skip)

If the user gives partial info (e.g., just "invoice for Acme Corp, 10 hours at $150"), fill in what you can and ask only about what's missing.

## Step 2: Build the Invoice

1. Read the HTML template from `assets/invoice-template.html` (bundled with this skill).
2. Replace all the placeholders with the actual data:
   - Contractor/company info from the config
   - Client info and line items from Step 1
   - Calculate each line total (quantity x unit price)
   - Calculate the subtotal and total due
   - Fill in invoice number, date, and payment terms
3. The template has these placeholders — replace them all:
   - `{{company_name}}`, `{{street_address}}`, `{{city_state_zip}}`, `{{phone}}`, `{{fax}}`, `{{email}}`
   - `{{contractor_name}}`
   - `{{client_name}}`, `{{client_email}}`
   - `{{invoice_number}}`, `{{invoice_date}}`
   - `{{line_items}}` — generate a `<tr>` for each item
   - `{{subtotal}}`, `{{total_due}}`
   - `{{payment_terms}}` — if provided, render the payment terms section; if not, remove it
   - `{{currency}}` — the currency symbol

Format all money amounts with two decimal places and the currency symbol.

## Step 3: Save and Present

1. Create the `/invoices` directory in the current working directory if it doesn't exist.
2. Save the invoice as `invoices/invoice-{{invoice_number}}.html` (sanitize the number for use as a filename — replace spaces and special characters with dashes).
3. Tell the user where the file was saved and open it in the browser:
   ```bash
   open invoices/invoice-XXX.html
   ```

The HTML file is fully self-contained (inline CSS, no external dependencies) and includes:
- A **print/download button** that triggers the browser's print dialog (which allows saving as PDF)
- The button is hidden when printing so it doesn't appear in the PDF

## Customizing the Template

If the user wants to change how invoices look, they can:

1. **Ask you to modify the template** — e.g., "add my logo", "change the colors to blue", "add a notes section". Read the current template, make the changes, and save it back.
2. **Edit it directly** — the template is at `assets/invoice-template.html` within the skill directory. It's standard HTML/CSS with `{{placeholder}}` markers.

When modifying the template, preserve all `{{placeholder}}` markers or update the skill logic to match any changes.
