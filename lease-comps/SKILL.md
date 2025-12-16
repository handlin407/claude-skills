---
name: lease-comps
description: Manages commercial real estate tenant lease agreements in Airtable's Lease Comps-HD database. Use when user provides lease information that includes tenant name, property address, leased space size, rental rate information, and lease timing/date. Handles address validation, duplicate checking (with fuzzy address matching), data formatting, and record creation following a structured 5-step workflow.
---

# Lease Comps Manager

## Overview

This skill manages commercial real estate lease comparables (tenant lease agreements) in Airtable. It follows a rigorous 5-step workflow to ensure data quality: parse incoming information → validate addresses → format details → check for duplicates with fuzzy matching → draft records → create in database.

## Airtable Database Reference

**Lease Comps-HD Table**
- Base ID: `apptqaigvuDe1izN6`
- Table ID: `tblbIOTTyKa0vJSdU`

**Required Fields:**
- `Address` - Full address with street, city, state, ZIP
- `Tenant` - Tenant/retailer name
- `Lease Details` - Leased space size and rental rate information
- `Lease Timing (LCD)` - Lease commencement date (quarter, year, or specific date)
- `Notes` - Optional additional details (lease structure, term, concessions, etc.)
- `City` - Extracted from address
- `Submarket` - From predefined list by city

**Auto-Generated Fields (DO NOT POPULATE):**
- `Record Modified Time (Auto)` - Automatically updated by database
- `Investor Portal` - Automatically managed
- `Created (Auto)` - Automatically set on creation
- `Verified` - Verification status field

## Core Workflow

### Step 1: Parse Incoming Information

Break down the lease data into the 7 required fields:
1. **Lease Timing (LCD)** - Lease commencement date (typically quarter like "Q1 2023", but may be year or specific date). Use the EARLIER of: announcement date OR lease begin date.
2. **Address** - Property address where tenant is leasing
3. **Tenant** - Tenant/retailer name
4. **Lease Details** - Leased space size (SF) and rental rate information ($/SF, total rent, etc.)
5. **Notes** - Lease structure, term length, lease concessions, tenant improvements, renewal options, etc.
6. **City** - From address
7. **Submarket** - Neighborhood/district classification

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

#### Lease Timing (LCD) Field
**Format:** Quarter-based preferred: `Q1 2023`, `Q2 2024`, etc.
- If specific date provided: Use format `Q1 2023` or `January 2023` or `1/15/2023`
- If only year provided: Use `2023`
- Use EARLIER of announcement date OR lease begin date
- Examples:
  - ✓ `Q3 2024`
  - ✓ `September 2024`
  - ✓ `9/15/2024`
  - ✓ `2024`

#### Lease Details Field
**Format:** Combine space size and rental rate information
- Always include comma separator for thousands
- Always use "SF" (uppercase) for square footage
- Include rental rate if available ($/SF/year or $/SF/month or total annual rent)
- Examples:
  - ✓ `5,000 SF at $85/SF/year`
  - ✓ `12,500 SF retail, $125/SF annual rent`
  - ✓ `3,200 SF at $22,000/month ($82.50/SF/year)`
  - ✗ `5000 sf` (missing comma, wrong case)

#### Notes Field
**Format:** Use semicolons to separate distinct points, line breaks for major sections
- Example structure:
```
15-year lease term;
10-year renewal option;
$500,000 tenant improvement allowance;
No rent for first 6 months (lease concession);
Percentage rent: 5% of sales over $5M threshold
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
2. Tenant names match exactly (case-sensitive, no normalization on tenant names)

**Important:** If either condition is different, it is NOT a duplicate.
- Same Tenant but different Address = NOT duplicate ✓
- Same Address but different Tenant = NOT duplicate ✓
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
Tenant: [tenant name]
Lease Details: [formatted lease details]
Lease Timing (LCD): [lease date]
Notes: [formatted notes]
City: [city name]
Submarket: [selected submarket]
```

Present to user and confirm: "Please review this draft. Reply 'approved' to create the record, or let me know what needs adjustment."

### Step 6: Create Record in Airtable

Once user confirms, use Airtable MCP `create_record` tool with:
- baseId: `apptqaigvuDe1izN6`
- tableId: `tblbIOTTyKa0vJSdU`
- fields: Object containing all 7 fields

After successful creation, provide brief confirmation: "✓ Lease comp created: [Tenant] leased [Lease Details] at [Address]"

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
- **Multi-location tenant:** Handle per-location or note "Multiple"

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
If any required field is missing (Lease Timing (LCD), Address, Tenant, Lease Details):
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
- **Tenant field:** Prefix with `[TEST]` (e.g., `[TEST] Nike`)
- **Notes field:** End with `[TEST-DELETE-MM/DD/YY]` (e.g., `[TEST-DELETE-12/16/24]`)

This allows filtering and bulk deletion of test records from the database.

## Additional Requests

### Retrieving Information
When asked to retrieve lease comps or data, use Airtable MCP `list_records` with appropriate filters and return relevant information.

### Updating Records
When asked to update an existing record, use Airtable MCP `update_records` with the record ID and updated fields.
