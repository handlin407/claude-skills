# Lease Comps Skill - Test Suite

**Purpose:** Validate the lease-comps skill handles all scenarios correctly before production use.

**IMPORTANT:** All test records are marked with:
- Tenant field: `[TEST]` prefix
- Notes field: `[TEST-DELETE-12/16/24]` suffix

**After testing:** Search Airtable for `[TEST]` to find and bulk-delete all test records.

---

## Test Case 1: Simple Retail Lease
**Purpose:** Validate basic workflow with complete data

**Input to provide:**
```
[TEST] Nike signed a new lease at 100 Lincoln Road in Miami Beach for 5,000 SF at $150/SF/year in Q3 2024.
Lease term is 10 years with a 5-year renewal option.
```

**Expected Behavior:**
- ✓ Should trigger lease-comps skill
- ✓ Address validated with ZIP: 100 Lincoln Road, Miami Beach, FL 33139
- ✓ Tenant: [TEST] Nike
- ✓ Lease Details: 5,000 SF at $150/SF/year
- ✓ Lease Timing (LCD): Q3 2024
- ✓ City extracted: Miami Beach
- ✓ Submarket inferred: Miami Beach
- ✓ Notes include: "10-year lease term; 5-year renewal option"
- ✓ No duplicates found
- ✓ Draft record shown for approval
- ✓ Record created successfully

**Fields to verify in draft:**
- Tenant: `[TEST] Nike`
- Notes should end with: `[TEST-DELETE-12/16/24]`

---

## Test Case 2: Office Lease with Complex Details
**Purpose:** Validate handling of detailed lease terms

**Input to provide:**
```
[TEST] Microsoft leased 50,000 SF at 1000 Brickell Avenue in Miami for $75/SF annually in Q1 2024.
Deal includes 20-year lease term, $2.5M tenant improvement allowance, 6 months free rent,
and percentage rent of 3% on revenue over $10M. Building is Class A office tower.
```

**Expected Behavior:**
- ✓ Lease Details: 50,000 SF at $75/SF/year
- ✓ Notes capture all details:
  - 20-year lease term
  - $2.5M TI allowance
  - 6 months free rent
  - 3% percentage rent over $10M
  - Class A office tower
- ✓ Submarket: Brickell

**Fields to verify:**
- Notes properly formatted with semicolons separating points
- All financial details captured

---

## Test Case 3: Missing Required Field
**Purpose:** Validate error handling for incomplete data

**Input to provide:**
```
[TEST] Apple opened a new store at 450 Park Avenue in Manhattan in Q4 2024.
The store is 8,000 SF flagship retail.
```

**Expected Behavior:**
- ✓ Skill triggers
- ✓ Identifies missing Lease Details (no rental rate provided)
- ✓ STOPS and asks: "I'm missing required information: Lease Details (rental rate). Please provide these before I proceed."
- ✓ Does NOT create draft or record

**Then provide:**
```
Rental rate is $300/SF/year
```

**Expected Behavior:**
- ✓ Continues with workflow
- ✓ Creates proper draft with 8,000 SF at $300/SF/year

---

## Test Case 4: Exact Duplicate Detection
**Purpose:** Validate fuzzy address matching catches duplicates

**Setup:** First create this record:
```
[TEST] Starbucks signed lease at 1450 Brickell Ave in Miami for 2,500 SF at $95/SF in Q1 2024.
10-year lease with 2 five-year renewal options.
```

**Then attempt to create (duplicate with fuzzy address):**
```
[TEST] Starbucks leased space at 1450 Brickell Avenue, Miami FL for 2,500 SF.
```

**Expected Behavior:**
- ✓ Address normalization should recognize:
  - "Brickell Ave" → "BRICKELL AVENUE"
  - "Brickell Avenue" → "BRICKELL AVENUE"
  - Normalized addresses match
- ✓ Tenant matches: "[TEST] Starbucks"
- ✓ STOPS and presents duplicate warning
- ✓ Shows existing record
- ✓ Asks: "Update existing? Create new? Skip?"

**User response:** "Skip"

**Expected Behavior:**
- ✓ Does not create duplicate record

---

## Test Case 5: Same Tenant, Different Location (NOT a Duplicate)
**Purpose:** Validate system allows same tenant at different address

**Setup:** Assumes Test Case 4 record exists ([TEST] Starbucks at 1450 Brickell Avenue)

**Input to provide:**
```
[TEST] Starbucks opened another location at 2000 Brickell Avenue in Miami for 1,800 SF at $90/SF in Q2 2024.
Drive-thru location with 15-year lease.
```

**Expected Behavior:**
- ✓ Address normalization:
  - Existing: "1450 BRICKELL AVENUE"
  - New: "2000 BRICKELL AVENUE"
  - Addresses do NOT match
- ✓ Same tenant but different address = NOT duplicate
- ✓ Proceeds with normal workflow
- ✓ Creates new record successfully

---

## Test Case 6: Address Validation Required
**Purpose:** Validate web search for missing ZIP codes

**Input to provide:**
```
[TEST] Whole Foods signed lease at 1 Lincoln Road in Miami Beach for 25,000 SF at $85/SF annually in Q3 2024.
Anchor tenant in lifestyle center.
```

**Expected Behavior:**
- ✓ Recognizes ZIP code is missing
- ✓ Uses web_search to find complete address
- ✓ Should find: 1 Lincoln Road, Miami Beach, FL 33139
- ✓ Uses validated address in record
- ✓ Submarket: Miami Beach

**Fields to verify:**
- Address includes full ZIP: 33139

---

## Test Case 7: Fuzzy Matching - Street Suffix Variations
**Purpose:** Validate normalization handles St vs Street variations

**Setup:** First create:
```
[TEST] Target leased 35,000 SF at 100 N Michigan Street, Chicago for $45/SF in Q1 2024.
Big box retail with parking.
```

**Then attempt (with "St" instead of "Street"):**
```
[TEST] Target signed lease at 100 North Michigan St in Chicago for 35,000 SF.
```

**Expected Behavior:**
- ✓ Address normalization:
  - Existing: "100 N MICHIGAN STREET"
  - New: "100 N MICHIGAN STREET" (Street/St → STREET, North/N → N)
  - Addresses match after normalization
- ✓ Same tenant
- ✓ STOPS with duplicate warning
- ✓ Asks for user decision

**User response:** "Skip"

---

## Test Case 8: Lease with Percentage Rent
**Purpose:** Validate handling of complex rental structures

**Input to provide:**
```
[TEST] Tesla opened showroom at 5000 Collins Avenue in Sunny Isles Beach, Florida.
Leased 12,000 SF at base rent $120/SF/year plus 4% of gross sales over $8M threshold.
Q4 2024 opening, 12-year lease term.
```

**Expected Behavior:**
- ✓ Lease Details: 12,000 SF at $120/SF/year + 4% of sales over $8M
- ✓ Notes include percentage rent structure
- ✓ Notes include 12-year term
- ✓ Address validated with ZIP
- ✓ Custom submarket accepted: Sunny Isles Beach

**Fields to verify:**
- Lease Details capture both base and percentage rent
- Submarket: Sunny Isles Beach (custom value)

---

## Test Case 9: Restaurant Lease with Monthly Rent
**Purpose:** Validate conversion/handling of monthly rent format

**Input to provide:**
```
[TEST] Carbone restaurant signed lease at 49 Collins Avenue, Miami Beach for 3,500 SF at $18,000/month in Q2 2024.
15-year lease with $1.2M buildout allowance. Ground floor corner location.
```

**Expected Behavior:**
- ✓ Lease Details should include monthly rent: 3,500 SF at $18,000/month ($61.71/SF/year)
  OR just: 3,500 SF at $18,000/month
- ✓ Notes include: 15-year lease term; $1.2M buildout allowance; Ground floor corner
- ✓ Submarket: Miami Beach

**Fields to verify:**
- Lease Details properly formatted
- TI allowance in notes with proper formatting: $1,200,000 or $1.2M

---

## Test Case 10: Lease Expansion/Renewal
**Purpose:** Validate handling of lease expansions vs new leases

**Setup:** First create:
```
[TEST] Lululemon has existing lease at 800 Lincoln Road, Miami Beach for 4,000 SF at $140/SF.
```

**Input to provide (expansion):**
```
[TEST] Lululemon expanded their space at 800 Lincoln Road, Miami Beach.
Added adjacent 2,000 SF at $150/SF in Q3 2024.
Total space now 6,000 SF. New 10-year term for expanded space.
```

**Expected Behavior:**
- ✓ This is NOT a duplicate (it's an expansion/modification)
- ✓ User should clarify: Create new record or update existing?
- ✓ If new record: Lease Details should clarify it's an expansion
- ✓ Notes should reference: "Expansion of existing space; Previous: 4,000 SF; Added: 2,000 SF"

---

## Test Case 11: Multi-Floor Lease
**Purpose:** Validate handling of multi-floor lease details

**Input to provide:**
```
[TEST] WeWork leased 75,000 SF at 1111 Brickell Avenue in Miami in Q1 2024.
Floors 10-13 (4 floors) at $68/SF/year. 15-year lease with $5M TI package.
Includes rooftop terrace access.
```

**Expected Behavior:**
- ✓ Lease Details: 75,000 SF (Floors 10-13) at $68/SF/year
- ✓ Notes capture:
  - 4 floors total
  - 15-year lease term
  - $5M TI package
  - Rooftop terrace access
- ✓ Submarket: Brickell

---

## Test Case 12: Pop-Up/Short-Term Lease
**Purpose:** Validate handling of short-term/unconventional leases

**Input to provide:**
```
[TEST] Glossier signed 18-month pop-up lease at 140 NE 39th Street in Miami Design District for 2,800 SF at $175/SF annually.
Q3 2024. Month-to-month after initial term. Ground floor high-visibility location.
```

**Expected Behavior:**
- ✓ Lease Details: 2,800 SF at $175/SF/year
- ✓ Notes include:
  - 18-month initial term
  - Month-to-month option after
  - Pop-up store
  - Ground floor high-visibility
- ✓ Submarket: Design District

---

## Testing Checklist

### Before Testing
- [ ] Ensure Airtable MCP is connected
- [ ] Have Lease Comps-HD table open in another tab
- [ ] Note current record count

### During Testing  
- [ ] Run each test case in order
- [ ] Verify expected behavior at each step
- [ ] Document any deviations or issues
- [ ] Confirm [TEST] markers are present in all records

### After Testing
- [ ] Search Airtable for "[TEST]" prefix
- [ ] Verify all test records are found
- [ ] Bulk delete all [TEST] records
- [ ] Confirm Lease Comps-HD returns to original record count

### Issues Log
Document any issues encountered:
```
Test Case #: 
Issue: 
Expected: 
Actual: 
Resolution: 
```

---

## Success Criteria

The skill passes testing if:
1. ✓ All 12 test cases execute as expected
2. ✓ Fuzzy address matching catches all duplicate variations
3. ✓ Error handling stops workflow when required
4. ✓ All records are properly formatted
5. ✓ Test records are easily identifiable and deletable
6. ✓ No production data is affected

## Post-Testing Cleanup

**To remove all test data:**

1. In Airtable, filter Lease Comps-HD table:
   - Filter: "Tenant contains [TEST]"
   
2. Select all filtered records

3. Delete selected records

4. Verify count returns to pre-test baseline

**Alternative:** Filter by Notes field containing "[TEST-DELETE-12/16/24]" if some test Tenant fields were not marked.