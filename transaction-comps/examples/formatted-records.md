# Transaction Comps - Formatted Record Examples

These examples demonstrate proper formatting for all fields in the Transaction Comps database.

## Example 1: Simple Single-Property Purchase

**Input:** "Blackstone acquired 450 Park Avenue in Manhattan for $500M in Q4 2023. The building is 600,000 SF office tower."

**Formatted Record:**
```
Address: 450 Park Avenue, New York, NY 10022
Price: $500,000,000
Size: 600,000 SF office tower
Purchaser: Blackstone
Date: Q4 2023
Notes: Office tower acquisition
City: New York
Submarket: Madison / 5th Ave
```

## Example 2: Transaction with Detailed Notes

**Input:** "Brookfield purchased 1450 Brickell Avenue in Miami for $125M, 7.2% cap rate. Seller was Crescent Heights looking to exit after lease-up. Property is 300,000 SF mixed-use with ground floor retail and office above."

**Formatted Record:**
```
Address: 1450 Brickell Avenue, Miami, FL 33131
Price: $125,000,000
Size: 300,000 SF mixed-use (ground floor retail + office above)
Purchaser: Brookfield
Date: Q2 2024
Notes: Seller: Crescent Heights seeking exit after lease-up;
Cap rate: 7.2%;
Mixed-use property with ground floor retail and office floors
City: Miami
Submarket: Brickell
```

## Example 3: Portfolio Transaction

**Input:** "CBRE Investment Management bought a 3-property retail portfolio in South Florida for $86M total. Properties are: 1) Aventura Mall outparcel (15,000 SF), 2) Las Olas retail center (45,000 SF), 3) Coconut Grove corner (8,500 SF). Seller was local family office."

**Formatted Record:**
```
Address: Multiple locations (see Notes)
Price: $86,000,000
Size: 68,500 SF total retail across 3 properties
Purchaser: CBRE Investment Management
Date: 2024
Notes: Portfolio acquisition - 3 properties;
Property 1: Aventura Mall outparcel, 15,000 SF, Aventura;
Property 2: Las Olas retail center, 45,000 SF, Fort Lauderdale;
Property 3: Coconut Grove corner, 8,500 SF, Miami;
Seller: Local family office
City: Multiple
Submarket: Multiple
```

## Example 4: Development Site

**Input:** "Related Companies paid $45M for the development site at 1234 West Loop Drive in Chicago. It's 2.5 acres zoned for mixed-use development, currently vacant land."

**Formatted Record:**
```
Address: 1234 West Loop Drive, Chicago, IL 60661
Price: $45,000,000
Size: 2.5 acres development site
Purchaser: Related Companies
Date: 2024
Notes: Vacant land zoned for mixed-use development;
Currently vacant
City: Chicago
Submarket: Fulton Market / West Loop
```

## Example 5: Transaction with Complex Financing

**Input:** "Starwood Capital purchased Lincoln Road retail building in Miami Beach for $210M, Q1 2024. Property is 85,000 SF flagship retail with tenants including Apple and Tesla. Deal was 65% LTV with Wells Fargo providing senior debt. In-place NOI is $14.7M representing 7.0% cap rate."

**Formatted Record:**
```
Address: 1111 Lincoln Road, Miami Beach, FL 33139
Price: $210,000,000
Size: 85,000 SF flagship retail
Purchaser: Starwood Capital
Date: Q1 2024
Notes: Flagship retail building on Lincoln Road;
Tenants: Apple flagship, Tesla showroom;
Financing: 65% LTV, Wells Fargo senior debt;
In-place NOI: $14.7M (7.0% cap rate)
City: Miami Beach
Submarket: Miami Beach
```

## Formatting Best Practices

### Price Formatting
- Always use dollar signs: `$XX,XXX,XXX`
- Alternative: `$XX.XM` for millions
- Include commas or M suffix, never plain numbers
- ✓ Good: `$30,000,000` or `$30.0M`
- ✗ Bad: `30000000` or `$30M` (missing decimal)

### Size Formatting
- Always include comma separators: `X,XXX SF`
- Use uppercase "SF"
- Include breakdown for complex properties
- ✓ Good: `45,000 SF (15,000 SF ground + 30,000 SF upper)`
- ✗ Bad: `45000 sf` or `45000SF`

### Notes Formatting
- Use semicolons to separate distinct points
- Use line breaks for major category changes
- Be specific with details (cap rates, tenant names, financing terms)
- Front-load most important information

### Address Formatting
- Always include ZIP code
- Format: Street, City, State ZIP
- Use proper state abbreviations (FL, NY, CA, etc.)

## Test Record Markers

For testing purposes, mark records as follows:

**Purchaser field:**
```
[TEST] Acme Capital
```

**Notes field (end with):**
```
[TEST-DELETE-12/16/24]
```

This allows easy identification and bulk deletion of test records.