---
name: transaction-comps
description: Manages commercial real estate property sales and acquisition transactions in Airtable's Transaction Comps-HD database. Use when user provides property purchase, sale, or acquisition information that includes purchaser/buyer name, property address, sale price, building size, and transaction date. Handles address validation, duplicate checking (with fuzzy address matching), data formatting, and record creation following a structured 5-step workflow.
---

# Transaction Comps Manager

## Overview

This skill manages commercial real estate transaction comparables (sales and acquisitions) in Airtable. It follows a rigorous 5-step workflow to ensure data quality: parse incoming information → validate addresses → format details → check for duplicates with fuzzy matching → draft records → create in database.

## Airtable Database Reference

**Transaction Comps-HD Table**
- Base ID: `appgcNuJlSybJQ0Lf`
- Table ID: `tblVWAXsv9KxphqdC`

**Required Fields:**
- `Address` - Full address with street, city, state, ZIP
- `Price` - Sale price (integer format)
- `Size` - Square footage with details
- `Purchaser` - Buyer/purchaser name
- `Date` - Transaction date (quarter, year, or specific date)
- `Notes` - Optional additional details
- `City` - Extracted from address
- `Submarket` - From predefined list by city

**Auto-Generated Fields (DO NOT POPULATE):**
- `Record Modified Time (Auto)` - Automatically updated by database

## Core Workflow

### Step 1: Parse Incoming Information

Break down the transaction data into the 8 required fields:
1. **Date** - Transaction closing date or announcement date (typically quarter like "Q1 2023", but may be year or specific date)
2. **Address** - Property address
3. **Price** - Sale price paid
4. **Size** - Square footage details
5. **Purchaser** - Buyer/purchaser entity name
6. **Notes** - Seller, financing, cap rate, NOI, tenants, etc.
7. **City** - From address
8. **Submarket** - Neighborhood/district classification

If any required field is missing, STOP and ask user: "I'm missing required information: [list missing fields]. Please provide these before I proceed."

### Step 2: Address Validation

**Critical: Every address MUST include street, city, state, and ZIP code.**

**Validation Process:**
1. Check if user's input includes ZIP code
2. If ZIP code missing, use `web_search` to find complete address
   - Search query: "[property address] [city] [state] zip code"
   - Verify address is correct
3. If address cannot be validated:
   - STOP and ask: "I cannot confirm the full address. Please provide the ZIP code or verify the address."
4. Use the validated complete address for all subsequent steps

**Override Protocol:**
- If user explicitly states "skip validation" or "use address as provided"
- Proceed with address as given
- Document in Notes: "Address not validated per user instruction"

### Step 3: Format Details

Apply proper formatting to all fields:

#### Price Field
**Format:** `$XX,XXX,XXX` OR `$XX.XM`
- Always include dollar sign
- Use commas for full numbers OR M suffix with one decimal
- Examples:
  - ✓ `$30,000,000`
  - ✓ `$30.0M`
  - ✗ `30000000` (missing $ and commas)
  - ✗ `$30M` (missing decimal for millions)

#### Size Field
**Format:** `X,XXX SF` with breakdown if applicable
- Always include comma separator for thousands
- Always use "SF" (uppercase)
- Include floor-by-floor breakdown if multiple floors
- Examples:
  - ✓ `45,000 SF (15,000 SF ground floor + 30,000 SF upper floors)`
  - ✓ `2,500 SF`
  - ✗ `2500 sf` (missing comma, wrong case)

#### Notes Field
**Format:** Use semicolons to separate distinct points, line breaks for major sections
- Example structure:
```
Seller: ABC Realty seeking 1031 exchange;
Buyer: XYZ Capital, 75% LTV financing;
NOI: $2.1M (7.0% cap rate);
Tenants: Nike flagship (15-year lease), Starbucks (10-year lease)
```

### Step 4: Duplicate Detection with Fuzzy Address Matching

**This is CRITICAL. Follow this protocol exactly.**

#### Retrieve All Records
Use Airtable MCP `list_records` with baseId and tableId to get ALL existing records from the table.

#### Address Normalization Rules
Before comparing addresses, normalize BOTH the existing record's address AND the new address using these rules:

1. **Convert to uppercase**
2. **Remove extra spaces** (multiple spaces → single space, trim ends)
3. **Standardize street suffixes:**
   - Street, St, St. → STREET
   - Avenue, Ave, Ave. → AVENUE
   - Road, Rd, Rd. → ROAD
   - Drive, Dr, Dr. → DRIVE
   - Boulevard, Blvd, Blvd. → BOULEVARD
   - Lane, Ln, Ln. → LANE
   - Court, Ct, Ct. → COURT
   - Place, Pl, Pl. → PLACE
   - Way, Wy → WAY
4. **Standardize directional prefixes:**
   - North, N, N. → N
   - South, S, S. → S
   - East, E, E. → E
   - West, W, W. → W
   - Northeast, NE, N.E. → NE
   - Northwest, NW, N.W. → NW
   - Southeast, SE, S.E. → SE
   - Southwest, SW, S.W. → SW
5. **Standardize unit designations:**
   - Suite, Ste, Ste., STE → SUITE
   - Unit, Apt, Apartment, # → UNIT
6. **Remove periods** from abbreviations after standardization

#### Duplicate Detection Logic
A duplicate exists when **BOTH** conditions are true:
1. Normalized addresses match exactly
2. Purchaser names match exactly (case-sensitive, no normalization on names)

**Important:** If either condition is different, it is NOT a duplicate.
- Same Purchaser but different Address = NOT duplicate ✓
- Same Address but different Purchaser = NOT duplicate ✓
- Both match = DUPLICATE ⚠️

#### If Duplicate Found
STOP and present to user:
1. Show the existing record details
2. Highlight what matches and what differs
3. Ask: "This appears to be a duplicate. Would you like to:
   - Update the existing record with new information?
   - Create a new record anyway?
   - Skip this entry?"
4. Wait for explicit user decision before proceeding

**Example Normalization:**
- New: "123 N Main St, Suite 4B"
- Existing: "123 North Main Street, Ste 4B"
- Normalized both: "123 N MAIN STREET SUITE 4B"
- Result: MATCH → Duplicate detected

### Step 5: Draft Record in Artifact

Create an artifact showing the complete formatted record with exact field names as they appear in Airtable:

```
Address: [full validated address]
Price: [formatted price]
Size: [formatted size]
Purchaser: [purchaser name]
Date: [transaction date]
Notes: [formatted notes]
City: [city name]
Submarket: [selected submarket]
```

Present to user and confirm: "Please review this draft. Reply 'approved' to create the record, or let me know what needs adjustment."

### Step 6: Create Record in Airtable

Once user confirms, use Airtable MCP `create_record` tool with:
- baseId: `appgcNuJlSybJQ0Lf`
- tableId: `tblVWAXsv9KxphqdC`
- fields: Object containing all 8 fields

After successful creation, provide brief confirmation: "✓ Transaction comp created: [Purchaser] acquired [Address] for [Price]"

## Submarket Selection

### Selection Logic

**Step 1: Check if explicitly stated**
- If user message includes submarket name → use it
- If user says "no submarket" or "skip submarket" → leave blank

**Step 2: Infer from validated address**
- Use the full validated address (especially neighborhood/district)
- Match against approved submarket list for that city
- If confident match (90%+) → select it
- If uncertain → go to Step 3

**Step 3: Ask for clarification**
Present: "This property at [address] appears to be in [inferred submarket]. Is this correct?"
- If user confirms → proceed
- If user corrects → use their submarket
- If user says "not sure" → provide list of options for that city

**Special Cases:**
- **Property spans multiple submarkets:** Use primary/dominant location
- **Property outside listed submarkets:** User may provide custom submarket name
- **Portfolio across multiple markets:** Handle per-property or note "Multiple"

### Submarket Classifications by City

#### Chicago
Gold Coast, Fulton Market / West Loop, Southport, Lincoln Park, Loop, Michigan Ave, Wicker Park / Bucktown, River North

#### South Florida
Brickell, Coconut Grove, Design District, Wynwood, Palm Beach, Miami Beach, Aventura, Bal Harbour, Worldcenter, Naples, Las Olas

#### New York
Soho, Madison / 5th Ave, Williamsburg

#### Boston
Back Bay, Southie, Seaport, Cambridge, Brookline, Wellesley

#### San Francisco
Union Square, Hayes Valley, Fillmore St, Chestnut / Marina

#### Los Angeles
Abbot Kinney, West Hollywood, Beverly Hills / Rodeo Drive, Silverlake, Santa Monica, Pasadena, Manhattan Beach, Highland Park

#### Austin
South Congress, Downtown, Rainey Street

#### Atlanta
Virginia-Highland, Buckhead, Morningside

#### Charlotte
South End

#### Houston
River Oaks

#### Orlando
Park Ave, Hannibal Square, Winter Park Village, Millenia

#### Dallas
Henderson Ave

#### Washington DC
Georgetown, Old Town Alexandria, CityCenterDC

## Error Handling

### Missing Required Information
If any required field is missing (Date, Address, Purchaser, Price, Size):
- STOP immediately
- List missing fields: "I'm missing required information: [list fields]. Please provide these before I proceed."

### Address Validation Failure
If `web_search` returns no results or ambiguous results:
- STOP and ask: "I couldn't validate the full address for [address]. Please provide:
  - The complete street address
  - ZIP code
  Or confirm you want to proceed with the address as stated."

### Duplicate Detected
If duplicate found during Step 4:
1. Present existing record to user
2. Show what's identical and what's different
3. Provide three options (Update / Create New / Skip)
4. Do not proceed without explicit user decision

### Airtable API Errors
If `create_record` or `list_records` fails:
1. Show the error message to user
2. Ask if they want to retry
3. Don't proceed to next record until current is resolved

### Submarket Ambiguity
If address doesn't clearly match a submarket:
- Ask: "This property at [address] could be in [option 1] or [option 2]. Which submarket should I use?"
- Provide list of options for that city

## Testing Protocol

**For test records, use these markers for easy identification and deletion:**
- **Purchaser field:** Prefix with `[TEST]` (e.g., `[TEST] Acme Capital`)
- **Notes field:** End with `[TEST-DELETE-MM/DD/YY]` (e.g., `[TEST-DELETE-12/16/24]`)

This allows filtering and bulk deletion of test records from the database.

## Additional Requests

### Retrieving Information
When asked to retrieve comps or data, use Airtable MCP `list_records` with appropriate filters and return relevant information.

### Updating Records
When asked to update an existing record, use Airtable MCP `update_records` with the record ID and updated fields.