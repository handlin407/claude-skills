# Tenant Sales Skill - Test Suite

**Purpose:** Validate the tenant-sales skill handles all scenarios correctly before production use.

**IMPORTANT:** All test records are marked with:
- Tenant field: `[TEST]` prefix
- Notes field: `[TEST-DELETE-12/16/24]` suffix

**After testing:** Search Airtable for `[TEST]` to find and bulk-delete all test records.

---

## Test Case 1: Simple Retail Sales Data
**Purpose:** Validate basic workflow with complete data

**Input to provide:**
```
[TEST] Apple Store at 100 Lincoln Road in Miami Beach generated $18.5M in annual sales in 2024.
The store is 12,000 SF flagship retail location.
```

**Expected Behavior:**
- ✓ Should trigger tenant-sales skill
- ✓ Address validated with ZIP: 100 Lincoln Road, Miami Beach, FL 33139
- ✓ Tenant: [TEST] Apple Store
- ✓ Year: 2024
- ✓ Sales Detail: $18,500,000 annual sales ($1,542/SF)
- ✓ City extracted: Miami Beach
- ✓ Submarket inferred: Miami Beach
- ✓ Notes include: "12,000 SF flagship retail location"
- ✓ No duplicates found
- ✓ Draft record shown for approval
- ✓ Record created successfully

**Fields to verify in draft:**
- Tenant: `[TEST] Apple Store`
- Notes should end with: `[TEST-DELETE-12/16/24]`

---

## Test Case 2: Restaurant Sales with Occupancy Cost
**Purpose:** Validate handling of detailed sales metrics

**Input to provide:**
```
[TEST] Carbone restaurant at 49 Collins Avenue, Miami Beach did $8.2M in sales in 2023.
Unit is 4,500 SF with rent of $180/SF/year. Occupancy cost is 9.9% of gross sales.
Average check is $125 per person with 180 covers per night.
```

**Expected Behavior:**
- ✓ Sales Detail: $8,200,000 annual sales ($1,822/SF)
- ✓ Notes capture all details:
  - Unit: 4,500 SF
  - Rent: $180/SF/year
  - Occupancy cost: 9.9% of gross sales
  - Average check: $125/person
  - Covers: 180/night
- ✓ Submarket: Miami Beach

**Fields to verify:**
- Notes properly formatted with semicolons separating points
- All performance metrics captured

---

## Test Case 3: Missing Required Field
**Purpose:** Validate error handling for incomplete data

**Input to provide:**
```
[TEST] Nike store at 450 Park Avenue in Manhattan generated strong sales.
The flagship store is 15,000 SF.
```

**Expected Behavior:**
- ✓ Skill triggers
- ✓ Identifies missing Year and Sales Detail
- ✓ STOPS and asks: "I'm missing required information: Year, Sales Detail. Please provide these before I proceed."
- ✓ Does NOT create draft or record

**Then provide:**
```
Year was 2024. Sales were $22M.
```

**Expected Behavior:**
- ✓ Continues with workflow
- ✓ Creates proper draft with Year: 2024, Sales Detail: $22,000,000 annual sales

---

## Test Case 4: Exact Duplicate Detection (All 3 Match)
**Purpose:** Validate duplicate detection when Tenant AND Address AND Year match

**Setup:** First create this record:
```
[TEST] Starbucks at 1450 Brickell Ave in Miami generated $1.8M in sales in 2023.
Drive-thru location, 2,500 SF.
```

**Then attempt to create (duplicate with fuzzy address):**
```
[TEST] Starbucks location at 1450 Brickell Avenue, Miami FL did $1.8M in 2023.
```

**Expected Behavior:**
- ✓ Address normalization should recognize:
  - "Brickell Ave" → "BRICKELL AVENUE"
  - "Brickell Avenue" → "BRICKELL AVENUE"
  - Normalized addresses match
- ✓ Tenant matches: "[TEST] Starbucks"
- ✓ Year matches: 2023
- ✓ STOPS and presents duplicate warning
- ✓ Shows existing record
- ✓ Asks: "Update existing? Create new? Skip?"

**User response:** "Skip"

**Expected Behavior:**
- ✓ Does not create duplicate record

---

## Test Case 5: Same Tenant & Address, Different Year (NOT a Duplicate)
**Purpose:** Validate system allows same tenant/address for different year

**Setup:** Assumes Test Case 4 record exists ([TEST] Starbucks at 1450 Brickell Avenue, 2023)

**Input to provide:**
```
[TEST] Starbucks at 1450 Brickell Avenue in Miami generated $2.1M in sales in 2024.
Sales increased 16.7% year-over-year.
```

**Expected Behavior:**
- ✓ Normalized addresses match
- ✓ Tenant matches
- ✓ Year DOES NOT match (2024 vs 2023)
- ✓ Same tenant/address but different year = NOT duplicate
- ✓ Proceeds with normal workflow
- ✓ Creates new record successfully
- ✓ Notes capture YoY growth

---

## Test Case 6: Address Validation Required
**Purpose:** Validate web search for missing ZIP codes

**Input to provide:**
```
[TEST] Whole Foods at 1 Lincoln Road in Miami Beach generated $45M in sales in 2024.
Flagship location with 35,000 SF grocery store.
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
[TEST] Target at 100 N Michigan Street, Chicago generated $32M in sales in 2024.
Big box retail, 45,000 SF.
```

**Then attempt (with "St" instead of "Street"):**
```
[TEST] Target store at 100 North Michigan St in Chicago did $32M in 2024.
```

**Expected Behavior:**
- ✓ Address normalization:
  - Existing: "100 N MICHIGAN STREET"
  - New: "100 N MICHIGAN STREET" (Street/St → STREET, North/N → N)
  - Addresses match after normalization
- ✓ Same tenant
- ✓ Same year (2024)
- ✓ STOPS with duplicate warning
- ✓ Asks for user decision

**User response:** "Skip"

---

## Test Case 8: Multi-Unit Performance Data
**Purpose:** Validate handling of complex unit configurations

**Input to provide:**
```
[TEST] Nike at 5000 Collins Avenue in Sunny Isles Beach, Florida.
Two-level flagship: 8,000 SF ground floor + 4,000 SF mezzanine = 12,000 SF total.
2024 sales: $16.8M total ($1,400/SF). Ground floor: $11.2M ($1,400/SF). Mezzanine: $5.6M ($1,400/SF).
```

**Expected Behavior:**
- ✓ Sales Detail: $16,800,000 annual sales ($1,400/SF)
- ✓ Notes include breakdown by level
- ✓ Address validated with ZIP
- ✓ Custom submarket accepted: Sunny Isles Beach

**Fields to verify:**
- Sales Detail captures total
- Notes include ground/mezzanine breakdown
- Submarket: Sunny Isles Beach (custom value)

---

## Test Case 9: Sales Per SF Only
**Purpose:** Validate handling when only sales/SF metric provided

**Input to provide:**
```
[TEST] Lululemon at 800 Lincoln Road, Miami Beach generated $1,850/SF in sales in 2024.
Store is 6,000 SF, which equals approximately $11.1M in total annual sales.
```

**Expected Behavior:**
- ✓ Sales Detail: $1,850/SF in annual sales ($11,100,000 total for 6,000 SF)
  OR: $11,100,000 annual sales ($1,850/SF)
- ✓ Notes include: 6,000 SF unit
- ✓ Submarket: Miami Beach

**Fields to verify:**
- Sales Detail properly formatted
- Total sales calculated and included

---

## Test Case 10: Multi-Location Aggregated Sales
**Purpose:** Validate handling of multi-store sales data

**Setup:** Consider this scenario:
```
[TEST] Starbucks operates 12 locations across South Florida generating combined $28.5M in sales in 2024.
Average sales per location: $2.375M. Top performing store: Aventura at $4.2M.
```

**Expected Behavior:**
- ✓ This is aggregated data across multiple locations
- ✓ Ask user: "Do you want to create:
  - Single record for aggregated data?
  - Separate records for each location (if addresses available)?"
- ✓ If aggregated: Address field: "Multiple locations (see Notes)"
- ✓ Notes should detail: "12 locations across South Florida; Combined sales: $28.5M; Average per location: $2.375M; Top performer: Aventura at $4.2M"

---

## Test Case 11: Restaurant with Detailed Performance Metrics
**Purpose:** Validate handling of comprehensive restaurant performance data

**Input to provide:**
```
[TEST] WeWork at 1111 Brickell Avenue in Miami generated $8.5M in membership revenue in 2024.
125,000 SF co-working space across 5 floors. 850 members. Average revenue per member: $10,000/year.
Occupancy: 92%. Revenue per SF: $68/SF.
```

**Expected Behavior:**
- ✓ Sales Detail: $8,500,000 annual revenue ($68/SF)
- ✓ Notes capture:
  - 125,000 SF across 5 floors
  - 850 members
  - Average revenue: $10,000/member/year
  - Occupancy: 92%
  - Co-working space
- ✓ Submarket: Brickell

---

## Test Case 12: Seasonal/Pop-Up Location
**Purpose:** Validate handling of temporary location sales data

**Input to provide:**
```
[TEST] Glossier pop-up at 140 NE 39th Street in Miami Design District for 18 months in 2024.
Generated $2.8M in sales over operating period. 3,200 SF. Annualized sales rate: $1,867/SF.
Pop-up opened Q2 2024, closed Q3 2024 (6 months actual operation).
```

**Expected Behavior:**
- ✓ Year: 2024
- ✓ Sales Detail: $2,800,000 total sales (6 months, $1,867/SF annualized rate)
- ✓ Notes include:
  - Pop-up location (temporary)
  - Operating period: Q2-Q3 2024 (6 months)
  - Annualized rate clarification
  - 3,200 SF
- ✓ Submarket: Design District

---

## Testing Checklist

### Before Testing
- [ ] Ensure Airtable MCP is connected
- [ ] Have Tenant Sales-HD table open in another tab
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
- [ ] Confirm Tenant Sales-HD returns to original record count

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
3. ✓ 3-way duplicate detection works (Tenant + Address + Year)
4. ✓ Error handling stops workflow when required
5. ✓ All records are properly formatted
6. ✓ Test records are easily identifiable and deletable
7. ✓ No production data is affected

## Post-Testing Cleanup

**To remove all test data:**

1. In Airtable, filter Tenant Sales-HD table:
   - Filter: "Tenant contains [TEST]"
   
2. Select all filtered records

3. Delete selected records

4. Verify count returns to pre-test baseline

**Alternative:** Filter by Notes field containing "[TEST-DELETE-12/16/24]" if some test Tenant fields were not marked.
