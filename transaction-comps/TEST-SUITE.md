# Transaction Comps Skill - Test Suite

**Purpose:** Validate the transaction-comps skill handles all scenarios correctly before production use.

**IMPORTANT:** All test records are marked with:
- Purchaser field: `[TEST]` prefix
- Notes field: `[TEST-DELETE-12/16/24]` suffix

**After testing:** Search Airtable for `[TEST]` to find and bulk-delete all test records.

---

## Test Case 1: Simple Single-Property Purchase
**Purpose:** Validate basic workflow with complete data

**Input to provide:**
```
[TEST] Acme Capital acquired 100 Biscayne Boulevard in Miami for $45 million in Q3 2024. 
The building is 75,000 SF office tower with ground floor retail.
```

**Expected Behavior:**
- ✓ Should trigger transaction-comps skill
- ✓ Address validated with ZIP: 100 Biscayne Boulevard, Miami, FL 33132
- ✓ Price formatted: $45,000,000
- ✓ Size formatted: 75,000 SF office tower with ground floor retail
- ✓ City extracted: Miami
- ✓ Submarket inferred: Brickell
- ✓ No duplicates found
- ✓ Draft record shown for approval
- ✓ Record created successfully

**Fields to verify in draft:**
- Purchaser: `[TEST] Acme Capital`
- Notes should end with: `[TEST-DELETE-12/16/24]`

---

## Test Case 2: Portfolio Acquisition
**Purpose:** Validate handling of multi-property transactions

**Input to provide:**
```
[TEST] Brookfield purchased a 3-property retail portfolio in South Florida for $125M total in 2024.
Properties are:
1) 500 Lincoln Road, Miami Beach - 20,000 SF
2) 200 Las Olas Boulevard, Fort Lauderdale - 35,000 SF  
3) 300 Miracle Mile, Coral Gables - 18,000 SF
Seller was local family office seeking liquidity.
```

**Expected Behavior:**
- ✓ Should handle as single record (per your preference)
- ✓ Address field: "Multiple locations (see Notes)" or list all
- ✓ Price: $125,000,000
- ✓ Size: 73,000 SF total retail across 3 properties
- ✓ Notes include all three addresses with details
- ✓ City: Multiple
- ✓ Submarket: Multiple
- ✓ Seller information captured in notes

**Fields to verify:**
- Purchaser: `[TEST] Brookfield`
- Notes include all property details and end with `[TEST-DELETE-12/16/24]`

---

## Test Case 3: Missing Required Field
**Purpose:** Validate error handling for incomplete data

**Input to provide:**
```
[TEST] Starwood Capital bought a property at 1000 Brickell Avenue in Miami in Q2 2024.
Building is 150,000 SF mixed-use.
```

**Expected Behavior:**
- ✓ Skill triggers
- ✓ Identifies missing Price field
- ✓ STOPS and asks: "I'm missing required information: Price. Please provide these before I proceed."
- ✓ Does NOT create draft or record

**Then provide:**
```
Price was $180M
```

**Expected Behavior:**
- ✓ Continues with workflow
- ✓ Creates proper draft with $180,000,000

---

## Test Case 4: Exact Duplicate Detection
**Purpose:** Validate fuzzy address matching catches duplicates

**Setup:** First create this record:
```
[TEST] Related Companies acquired 1450 Brickell Ave in Miami for $95M in Q1 2024.
Property is 200,000 SF office building.
```

**Then attempt to create (duplicate with fuzzy address):**
```
[TEST] Related Companies purchased 1450 Brickell Avenue, Miami FL for $95M.
Building is 200,000 SF office.
```

**Expected Behavior:**
- ✓ Address normalization should recognize:
  - "Brickell Ave" → "BRICKELL AVENUE"
  - "Brickell Avenue" → "BRICKELL AVENUE"
  - Normalized addresses match
- ✓ Purchaser matches: "[TEST] Related Companies"
- ✓ STOPS and presents duplicate warning
- ✓ Shows existing record
- ✓ Asks: "Update existing? Create new? Skip?"

**User response:** "Skip"

**Expected Behavior:**
- ✓ Does not create duplicate record

---

## Test Case 5: Near Duplicate (Different Address)
**Purpose:** Validate system allows same purchaser at different address

**Setup:** Assumes Test Case 4 record exists ([TEST] Related Companies at 1450 Brickell Avenue)

**Input to provide:**
```
[TEST] Related Companies bought 2000 Brickell Avenue in Miami for $110M in Q2 2024.
Property is 250,000 SF residential tower.
```

**Expected Behavior:**
- ✓ Address normalization:
  - Existing: "1450 BRICKELL AVENUE"
  - New: "2000 BRICKELL AVENUE"
  - Addresses do NOT match
- ✓ Same purchaser but different address = NOT duplicate
- ✓ Proceeds with normal workflow
- ✓ Creates new record successfully

---

## Test Case 6: Address Validation Required
**Purpose:** Validate web search for missing ZIP codes

**Input to provide:**
```
[TEST] Blackstone purchased 1 Lincoln Road in Miami Beach for $200M in 2024.
Building is 100,000 SF flagship retail.
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
[TEST] Tishman Speyer acquired 100 N Michigan Street, Chicago for $75M in Q1 2024.
Building is 120,000 SF office.
```

**Then attempt (with "St" instead of "Street"):**
```
[TEST] Tishman Speyer bought 100 North Michigan St in Chicago for $75M.
```

**Expected Behavior:**
- ✓ Address normalization:
  - Existing: "100 N MICHIGAN STREET"
  - New: "100 N MICHIGAN STREET" (Street/St → STREET, North/N → N)
  - Addresses match after normalization
- ✓ Same purchaser
- ✓ STOPS with duplicate warning
- ✓ Asks for user decision

**User response:** "Skip"

---

## Test Case 8: Custom Submarket
**Purpose:** Validate handling of properties outside predefined submarkets

**Input to provide:**
```
[TEST] Equity Residential purchased 5000 Collins Avenue in Sunny Isles Beach, Florida for $85M in 2024.
Property is 180,000 SF residential.
```

**Expected Behavior:**
- ✓ Address validated: 5000 Collins Avenue, Sunny Isles Beach, FL 33140
- ✓ Sunny Isles Beach not in predefined South Florida submarkets
- ✓ Should ask user about submarket
- ✓ User can provide custom: "Sunny Isles Beach"
- ✓ Accepts custom submarket value

**Fields to verify:**
- Submarket: Sunny Isles Beach (custom value accepted)

---

## Test Case 9: Very Large Transaction (>$100M)
**Purpose:** Validate formatting of large numbers

**Input to provide:**
```
[TEST] Citadel purchased 425 Park Avenue in Manhattan for $2.2 billion in Q4 2024.
Building is 670,000 SF trophy office tower with Madison Avenue frontage.
Previous owner was RXR Realty. Deal represents 4.5% cap rate.
```

**Expected Behavior:**
- ✓ Price formatted correctly: $2,200,000,000 OR $2.2B
- ✓ Size: 670,000 SF trophy office tower
- ✓ Notes capture seller and cap rate
- ✓ Submarket: Madison / 5th Ave

**Fields to verify:**
- Price properly formatted with commas or billion suffix
- Notes include: "Previous owner: RXR Realty; Cap rate: 4.5%; [TEST-DELETE-12/16/24]"

---

## Test Case 10: Mixed-Use Complex Property
**Purpose:** Validate handling of complex size descriptions

**Input to provide:**
```
[TEST] Hines Development acquired 1500 Wynwood Avenue in Miami for $62M in Q3 2024.
Property is mixed-use development: 25,000 SF ground floor retail, 75,000 SF office (floors 2-5),
50,000 SF residential (floors 6-10), 150 total units.
```

**Expected Behavior:**
- ✓ Size field captures full breakdown: "150,000 SF mixed-use (25,000 SF ground floor retail + 75,000 SF office floors 2-5 + 50,000 SF residential floors 6-10)"
- ✓ Notes include: "150 residential units"
- ✓ Submarket: Wynwood

---

## Test Case 11: Address Override Protocol
**Purpose:** Validate user can override address validation

**Input to provide:**
```
[TEST] Lincoln Property purchased new development site at Future Street (address TBD) in 
Fort Lauderdale for $25M in 2024. It's 3 acres of land. Skip validation - use address as provided.
```

**Expected Behavior:**
- ✓ Recognizes "skip validation" instruction
- ✓ Uses address as provided: "Future Street (address TBD), Fort Lauderdale, FL"
- ✓ Notes include: "Address not validated per user instruction"
- ✓ Proceeds with record creation

---

## Test Case 12: Fuzzy Matching - Directional Variations
**Purpose:** Validate normalization handles North vs N variations

**Setup:** First create:
```
[TEST] Prologis acquired 500 West Loop Drive in Chicago for $40M in 2024.
Property is 180,000 SF industrial warehouse.
```

**Then attempt (with "W" instead of "West"):**
```
[TEST] Prologis bought 500 W Loop Drive, Chicago for $40M.
```

**Expected Behavior:**
- ✓ Address normalization:
  - Existing: "500 W LOOP DRIVE"
  - New: "500 W LOOP DRIVE" (West → W, W → W)
  - Match detected
- ✓ Same purchaser
- ✓ Duplicate warning displayed

---

## Testing Checklist

### Before Testing
- [ ] Ensure Airtable MCP is connected
- [ ] Have Transaction Comps-HD table open in another tab
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
- [ ] Confirm Transaction Comps-HD returns to original record count

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

1. In Airtable, filter Transaction Comps-HD table:
   - Filter: "Purchaser contains [TEST]"
   
2. Select all filtered records

3. Delete selected records

4. Verify count returns to pre-test baseline

**Alternative:** Filter by Notes field containing "[TEST-DELETE-12/16/24]" if some test Purchaser fields were not marked.