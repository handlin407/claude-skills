# Lease Comps - Formatted Record Examples

These examples demonstrate proper formatting for all fields in the Lease Comps database.

## Example 1: Simple Retail Lease

**Input:** "Nike signed a 10-year lease at 450 Lincoln Road in Miami Beach for 5,000 SF at $150/SF annually in Q3 2024."

**Formatted Record:**
```
Address: 450 Lincoln Road, Miami Beach, FL 33139
Tenant: Nike
Lease Details: 5,000 SF at $150/SF/year
Lease Timing (LCD): Q3 2024
Notes: 10-year lease term
City: Miami Beach
Submarket: Miami Beach
```

## Example 2: Office Lease with Complex Terms

**Input:** "Microsoft leased 75,000 SF at 1450 Brickell Avenue in Miami for $72/SF/year in Q1 2024. Deal includes 20-year term, $3.5M tenant improvement allowance, 9 months free rent as concession, and 3 five-year renewal options. Floors 15-18 in Class A trophy tower."

**Formatted Record:**
```
Address: 1450 Brickell Avenue, Miami, FL 33131
Tenant: Microsoft
Lease Details: 75,000 SF (Floors 15-18) at $72/SF/year
Lease Timing (LCD): Q1 2024
Notes: 20-year lease term;
3 five-year renewal options;
$3,500,000 ($3.5M) tenant improvement allowance;
9 months free rent (lease concession);
Class A trophy office tower
City: Miami
Submarket: Brickell
```

## Example 3: Restaurant Lease with Percentage Rent

**Input:** "Carbone restaurant signed 15-year lease at 49 Collins Avenue, Miami Beach for 4,500 SF. Base rent is $180/SF/year plus 5% of gross sales exceeding $6M annually. Q2 2024 opening with $1.8M buildout allowance from landlord."

**Formatted Record:**
```
Address: 49 Collins Avenue, Miami Beach, FL 33139
Tenant: Carbone
Lease Details: 4,500 SF at $180/SF/year base rent + 5% of sales over $6M
Lease Timing (LCD): Q2 2024
Notes: 15-year lease term;
Percentage rent: 5% of gross sales exceeding $6M annually;
$1,800,000 ($1.8M) buildout allowance;
Restaurant use
City: Miami Beach
Submarket: Miami Beach
```

## Example 4: Multi-Location Lease Package

**Input:** "Starbucks signed leases for 3 South Florida locations in Q4 2024: 1) Design District - 2,200 SF at $125/SF, 2) Coconut Grove - 1,800 SF at $110/SF, 3) Bal Harbour - 2,500 SF at $165/SF. All 15-year terms with standardized build-outs."

**Formatted Record:**
```
Address: Multiple locations (see Notes)
Tenant: Starbucks
Lease Details: 6,500 SF total across 3 locations (avg $135/SF/year)
Lease Timing (LCD): Q4 2024
Notes: Multi-location lease package - 3 stores;
Location 1: Design District, 2,200 SF at $125/SF/year;
Location 2: Coconut Grove, 1,800 SF at $110/SF/year;
Location 3: Bal Harbour Shops, 2,500 SF at $165/SF/year;
All locations: 15-year lease term with standardized build-outs
City: Multiple
Submarket: Multiple
```

## Example 5: Lease Renewal/Expansion

**Input:** "Lululemon renewed and expanded their lease at 800 Lincoln Road, Miami Beach in Q1 2024. Original 4,000 SF expanded to 6,000 SF (added adjacent 2,000 SF) at new rate of $155/SF for entire space. New 12-year term, $800K for build-out of expansion area."

**Formatted Record:**
```
Address: 800 Lincoln Road, Miami Beach, FL 33139
Tenant: Lululemon
Lease Details: 6,000 SF at $155/SF/year (expansion from 4,000 SF)
Lease Timing (LCD): Q1 2024
Notes: Lease renewal and expansion;
Original space: 4,000 SF;
Expansion: Added 2,000 SF adjacent space;
New total: 6,000 SF;
New 12-year lease term;
$800,000 TI allowance for expansion build-out
City: Miami Beach
Submarket: Miami Beach
```

## Example 6: Pop-Up/Short-Term Lease

**Input:** "Glossier signed 24-month pop-up lease at 140 NE 39th Street in Miami Design District for 3,200 SF at $200/SF in Q3 2024. Month-to-month option after initial term. Landlord provided turnkey build-out."

**Formatted Record:**
```
Address: 140 NE 39th Street, Miami, FL 33137
Tenant: Glossier
Lease Details: 3,200 SF at $200/SF/year
Lease Timing (LCD): Q3 2024
Notes: 24-month pop-up lease (short-term);
Month-to-month option after initial term;
Turnkey build-out provided by landlord;
Ground floor high-visibility location
City: Miami
Submarket: Design District
```

## Example 7: Co-Working/Flexible Office Lease

**Input:** "WeWork leased entire floors 8-12 at 1111 Brickell Avenue in Miami, totaling 125,000 SF at $65/SF annually. Q2 2024, 20-year lease with $8M TI package. Includes rooftop terrace rights and dedicated elevator bank."

**Formatted Record:**
```
Address: 1111 Brickell Avenue, Miami, FL 33131
Tenant: WeWork
Lease Details: 125,000 SF (Floors 8-12, 5 floors) at $65/SF/year
Lease Timing (LCD): Q2 2024
Notes: 20-year lease term;
$8,000,000 ($8.0M) tenant improvement package;
Includes rooftop terrace access rights;
Dedicated elevator bank;
Co-working/flexible office use
City: Miami
Submarket: Brickell
```

## Formatting Best Practices

### Lease Timing (LCD) Formatting
- Preferred: Quarter format `Q1 2024`, `Q3 2024`
- Alternative: Month format `January 2024`, `September 2024`
- Alternative: Specific date `1/15/2024`, `9/30/2024`
- Alternative: Year only `2024`
- Use EARLIER of announcement date OR lease commencement date
- ✓ Good: `Q3 2024`, `September 2024`, `9/15/2024`
- ✗ Bad: `3rd quarter 2024`, `Fall 2024`

### Lease Details Formatting
- Always include comma separators: `X,XXX SF`
- Use uppercase "SF"
- Include rental rate structure clearly
- ✓ Good: `5,000 SF at $150/SF/year`
- ✓ Good: `12,500 SF at $95/SF/year + 4% of sales over $8M`
- ✓ Good: `3,200 SF at $22,000/month ($82.50/SF/year)`
- ✗ Bad: `5000 sf at 150 per sf`

### Notes Formatting
- Use semicolons to separate distinct points
- Use line breaks for major category changes
- Be specific with financial details
- Include lease term length
- Include any concessions or special terms
- Front-load most important information

### Address Formatting
- Always include ZIP code
- Format: Street, City, State ZIP
- Use proper state abbreviations (FL, NY, CA, etc.)

## Test Record Markers

For testing purposes, mark records as follows:

**Tenant field:**
```
[TEST] Nike
```

**Notes field (end with):**
```
[TEST-DELETE-12/16/24]
```

This allows easy identification and bulk deletion of test records.

## Common Lease Terms to Capture in Notes

- **Lease term length** (e.g., "10-year lease term")
- **Renewal options** (e.g., "2 five-year renewal options")
- **Tenant improvements** (e.g., "$500,000 TI allowance")
- **Rent concessions** (e.g., "6 months free rent")
- **Percentage rent** (e.g., "3% of sales over $5M")
- **Special rights** (e.g., "Exclusive use clause", "Rooftop access")
- **Lease type** (e.g., "NNN lease", "Modified gross")
- **Use restrictions** (e.g., "Restaurant use", "Retail only")
- **Parking** (e.g., "20 dedicated parking spaces")
- **Signage** (e.g., "Monument sign rights")
