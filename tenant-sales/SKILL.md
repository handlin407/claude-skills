---
name: tenant-sales
description: Manages commercial real estate tenant sales data in Airtable's Tenant Sales-HD database. Use when user provides tenant revenue/sales information that includes tenant name, property address, year, and sales figures (revenue, sales per SF, etc.). Handles address validation, duplicate checking (with fuzzy address matching), data formatting, and record creation following a structured 5-step workflow.
---

# Tenant Sales Manager

## Overview

This skill manages commercial real estate tenant sales data (revenue figures and performance metrics) in Airtable. It follows a rigorous 5-step workflow to ensure data quality: parse incoming information → validate addresses → format details → check for duplicates with fuzzy matching → draft records → create in database.

## Airtable Database Reference

**Tenant Sales-HD Table**
- Base ID: `app53DekPcYwDuV3E`
- Table ID: `tblahbVKhP8HFx7YQ`

**Required Fields:**
- `Address` - Full address with street, city, state, ZIP
- `Tenant` - Tenant/retailer name
- `Year` - Year the sales data relates to
- `Sales Detail` - Revenue figures, sales per SF, or other performance metrics
- `Notes` - Optional additional details (unit size, occupancy cost, lease details, etc.)
- `City` - Extracted from address
- `Submarket` - From predefined list by city

**Auto-Generated Fields (DO NOT POPULATE):**
- `Record Modified Time (Auto)` - Automatically updated by database

## Core Workflow

### Step 1: Parse Incoming Information

Break down the tenant sales data into the 7 required fields:
1. **Year** - The year the sales data relates to (e.g., "2024", "2023")
2. **Address** - Property address where tenant operates
3. **Tenant** - Tenant/retailer name
4. **Sales Detail** - Revenue figures, sales per SF, or performance metrics
5. **Notes** - Unit size, unit details, occupancy cost, lease details, etc.
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

#### Year Field
**Format:** Four-digit year: `2024`, `2023`, etc.
- Always use 4-digit format
- Examples:
  - ✓ `2024`
  - ✓ `2023`
  - ✗ `24` (incomplete)
  - ✗ `FY2024` (use just the year)

#### Sales Detail Field
**Format:** Clear revenue/sales figures with proper formatting
- Always include dollar signs for revenue
- Use comma separators for thousands
- Include context (annual sales, sales per SF, etc.)
- Examples:
  - ✓ `$12,500,000 annual revenue`
  - ✓ `$8.5M annual sales ($1,200/SF)`
  - ✓ `$850/SF in annual sales`
  - ✓ `$15.2M total revenue, 25,000 SF unit`
  - ✗ `12500000` (missing $ and commas)
  - ✗ `$12.5M` (needs context - annual? per SF?)

#### Notes Field
**Format:** Use semicolons to separate distinct points, line breaks for major sections
- Example structure:
```
Unit: 8,500 SF ground floor retail;
Occupancy cost: 18% of gross sales;
Lease: $145/SF/year base rent + 3% of sales over $5M;
Store opened Q2 2023
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
A duplicate exists when **ALL THREE** conditions are true:
1. Normalized addresses match exactly
2. Tenant names match exactly (case-sensitive, no normalization on tenant names)
3. Year matches exactly

**Important:** If ANY condition is different, it is NOT a duplicate.
- Same Tenant, same Address, but different Year = NOT duplicate ✓
- Same Tenant, same Year, but different Address = NOT duplicate ✓
- Same Address, same Year, but different Tenant = NOT duplicate ✓
- All three match = DUPLICATE ⚠️

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
- New: "123 N Main St, Miami Beach"
- Existing: "123 North Main Street, Miami Beach"
- Normalized both: "123 N MAIN STREET MIAMI BEACH"
- If Tenant AND Year also match → Duplicate detected

### Step 5: Draft Record in Artifact

Create an artifact showing the complete formatted record with exact field names as they appear in Airtable:

```
Address: [full validated address]
Tenant: [tenant name]
Year: [year]
Sales Detail: [formatted sales details]
Notes: [formatted notes]
City: [city name]
Submarket: [selected submarket]
```

Present to user and confirm: "Please review this draft. Reply 'approved' to create the record, or let me know what needs adjustment."

### Step 6: Create Record in Airtable

Once user confirms, use Airtable MCP `create_record` tool with:
- baseId: `app53DekPcYwDuV3E`
- tableId: `tblahbVKhP8HFx7YQ`
- fields: Object containing all 7 fields

After successful creation, provide brief confirmation: "✓ Tenant sales data created: [Tenant] at [Address] - [Year]: [Sales Detail]"

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
If any required field is missing (Year, Address, Tenant, Sales Detail):
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
- **Tenant field:** Prefix with `[TEST]` (e.g., `[TEST] Apple Store`)
- **Notes field:** End with `[TEST-DELETE-MM/DD/YY]` (e.g., `[TEST-DELETE-12/16/24]`)

This allows filtering and bulk deletion of test records from the database.

## Additional Requests

### Retrieving Information
When asked to retrieve tenant sales data, use Airtable MCP `list_records` with appropriate filters and return relevant information.

### Updating Records
When asked to update an existing record, use Airtable MCP `update_records` with the record ID and updated fields.
