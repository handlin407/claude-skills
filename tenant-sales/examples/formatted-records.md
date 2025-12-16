# Tenant Sales - Formatted Record Examples

These examples demonstrate proper formatting for all fields in the Tenant Sales database.

## Example 1: Simple Retail Sales

**Input:** "Apple Store at 450 Lincoln Road in Miami Beach generated $22.5M in annual sales in 2024. The store is 15,000 SF flagship retail."

**Formatted Record:**
```
Address: 450 Lincoln Road, Miami Beach, FL 33139
Tenant: Apple Store
Year: 2024
Sales Detail: $22,500,000 annual sales ($1,500/SF)
Notes: 15,000 SF flagship retail location
City: Miami Beach
Submarket: Miami Beach
```

## Example 2: Restaurant with Detailed Performance Metrics

**Input:** "Carbone restaurant at 49 Collins Avenue, Miami Beach did $9.8M in sales in 2024. Unit is 4,500 SF with annual rent of $810K ($180/SF). Occupancy cost is 8.3% of gross sales. Average check is $135 per person with 200 covers per night. Restaurant operates dinner only, 6 days/week."

**Formatted Record:**
```
Address: 49 Collins Avenue, Miami Beach, FL 33139
Tenant: Carbone
Year: 2024
Sales Detail: $9,800,000 annual sales ($2,178/SF)
Notes: Unit: 4,500 SF restaurant;
Annual rent: $810,000 ($180/SF/year);
Occupancy cost: 8.3% of gross sales;
Average check: $135/person;
Covers: 200/night;
Operations: Dinner only, 6 days/week
City: Miami Beach
Submarket: Miami Beach
```

## Example 3: Retailer Sales Productivity Metrics

**Input:** "Lululemon at 800 Lincoln Road, Miami Beach generated $11.1M in 2024 from their 6,000 SF store. Sales productivity is $1,850/SF, which ranks in top 10% of their nationwide portfolio. Store opened in Q2 2022. Lease is $155/SF/year with occupancy cost of 8.4%."

**Formatted Record:**
```
Address: 800 Lincoln Road, Miami Beach, FL 33139
Tenant: Lululemon
Year: 2024
Sales Detail: $11,100,000 annual sales ($1,850/SF)
Notes: 6,000 SF retail unit;
Sales productivity: Top 10% of nationwide portfolio;
Store opened: Q2 2022;
Lease: $155/SF/year;
Occupancy cost: 8.4% of gross sales
City: Miami Beach
Submarket: Miami Beach
```

## Example 4: Multi-Level Flagship Performance

**Input:** "Nike flagship at 1450 Brickell Avenue in Miami generated $24.6M in sales in 2024. Two-level store: 10,000 SF ground floor + 8,000 SF upper = 18,000 SF total. Ground floor: $16.4M ($1,640/SF). Upper level: $8.2M ($1,025/SF). Overall productivity: $1,367/SF."

**Formatted Record:**
```
Address: 1450 Brickell Avenue, Miami, FL 33131
Tenant: Nike
Year: 2024
Sales Detail: $24,600,000 annual sales ($1,367/SF for 18,000 SF)
Notes: Two-level flagship store;
Ground floor: 10,000 SF at $16.4M ($1,640/SF);
Upper level: 8,000 SF at $8.2M ($1,025/SF);
Total: 18,000 SF
City: Miami
Submarket: Brickell
```

## Example 5: Grocery/Specialty Food Retailer

**Input:** "Whole Foods at 1 Lincoln Road, Miami Beach generated $52M in sales in 2024. 35,000 SF flagship grocery store with prepared foods section. Average transaction: $67. Daily traffic: approximately 2,100 customers. Sales per transaction and traffic indicate strong local customer base."

**Formatted Record:**
```
Address: 1 Lincoln Road, Miami Beach, FL 33139
Tenant: Whole Foods
Year: 2024
Sales Detail: $52,000,000 annual sales ($1,486/SF)
Notes: 35,000 SF flagship grocery store with prepared foods;
Average transaction: $67;
Daily traffic: ~2,100 customers;
Strong local customer base
City: Miami
Submarket: Miami Beach
```

## Example 6: Co-Working Space Revenue

**Input:** "WeWork at 1111 Brickell Avenue in Miami generated $10.2M in membership revenue in 2024. 125,000 SF co-working space across floors 8-12 (5 floors). 950 members with average revenue of $10,737/member/year. Occupancy rate: 94%. Revenue per SF: $82/SF."

**Formatted Record:**
```
Address: 1111 Brickell Avenue, Miami, FL 33131
Tenant: WeWork
Year: 2024
Sales Detail: $10,200,000 annual revenue ($82/SF)
Notes: 125,000 SF co-working space (Floors 8-12, 5 floors);
950 members;
Average revenue: $10,737/member/year;
Occupancy rate: 94%;
Co-working/flexible office use
City: Miami
Submarket: Brickell
```

## Example 7: Year-Over-Year Growth Tracking

**Input:** "Starbucks at 1450 Brickell Avenue in Miami generated $2.4M in sales in 2024, up from $2.1M in 2023. That's 14.3% YoY growth. Drive-thru location, 2,500 SF. Average ticket increased from $8.50 to $9.25. Transaction count grew 6.8%."

**Formatted Record:**
```
Address: 1450 Brickell Avenue, Miami, FL 33131
Tenant: Starbucks
Year: 2024
Sales Detail: $2,400,000 annual sales ($960/SF)
Notes: Drive-thru location, 2,500 SF;
YoY growth: 14.3% (from $2.1M in 2023);
Average ticket: $9.25 (up from $8.50);
Transaction growth: 6.8%
City: Miami
Submarket: Brickell
```

## Formatting Best Practices

### Year Formatting
- Always use 4-digit year
- ✓ Good: `2024`, `2023`
- ✗ Bad: `24`, `FY2024`, `CY 2024`

### Sales Detail Formatting
- Always include dollar signs and commas
- Include context (annual sales, per SF, etc.)
- Calculate and show $/SF when possible
- ✓ Good: `$22,500,000 annual sales ($1,500/SF)`
- ✓ Good: `$8.5M annual revenue ($1,200/SF for 7,083 SF)`
- ✓ Good: `$1,850/SF in annual sales ($11.1M total)`
- ✗ Bad: `22500000` (missing formatting and context)
- ✗ Bad: `$22.5M` (needs context - per SF? total?)

### Notes Formatting
- Use semicolons to separate distinct points
- Use line breaks for major category changes
- Include unit size/configuration
- Include occupancy cost when available
- Include performance metrics (average check, traffic, etc.)
- Front-load most important information

### Address Formatting
- Always include ZIP code
- Format: Street, City, State ZIP
- Use proper state abbreviations (FL, NY, CA, etc.)

## Test Record Markers

For testing purposes, mark records as follows:

**Tenant field:**
```
[TEST] Apple Store
```

**Notes field (end with):**
```
[TEST-DELETE-12/16/24]
```

This allows easy identification and bulk deletion of test records.

## Common Metrics to Capture in Notes

- **Unit size** (e.g., "8,500 SF ground floor retail")
- **Unit configuration** (e.g., "Two-level: 6,000 SF ground + 4,000 SF mezzanine")
- **Occupancy cost** (e.g., "Occupancy cost: 12% of gross sales")
- **Lease details** (e.g., "Lease: $145/SF/year base rent")
- **Performance metrics** (e.g., "Average check: $95/person", "Daily traffic: 850 customers")
- **Year-over-year data** (e.g., "YoY growth: 8.5%")
- **Comparative ranking** (e.g., "Top 5% of brand's portfolio")
- **Operating characteristics** (e.g., "7 days/week, lunch + dinner service")
- **Seasonality** (e.g., "Q4 represents 40% of annual sales")

## Sales Detail Calculation Examples

### When Given Total Sales and Unit Size:
- Input: "$15M sales, 10,000 SF"
- Output: `$15,000,000 annual sales ($1,500/SF)`

### When Given Sales Per SF and Unit Size:
- Input: "$1,200/SF, 8,000 SF"
- Calculation: $1,200 × 8,000 = $9,600,000
- Output: `$9,600,000 annual sales ($1,200/SF)` OR `$1,200/SF in annual sales ($9.6M total for 8,000 SF)`

### When Given Multiple Levels:
- Input: "Ground: 5,000 SF at $1,800/SF = $9M; Upper: 3,000 SF at $1,200/SF = $3.6M"
- Total: 8,000 SF, $12.6M
- Blended: $12.6M ÷ 8,000 SF = $1,575/SF
- Output: `$12,600,000 annual sales ($1,575/SF for 8,000 SF)`

## Special Scenarios

### Pop-Up/Temporary Locations
- Clearly note temporary status
- Include operating period
- If partial year, note actual months and show annualized rate separately

### Multi-Location Aggregates
- If providing combined sales across multiple locations
- Use Address: "Multiple locations (see Notes)"
- Detail each location in Notes with its contribution

### Percentage Rent Locations
- Include base rent and percentage rent structure
- Show natural breakpoint if available
- Note what percentage of sales comes from percentage rent vs base

### Co-Working/Membership Models
- Revenue per member metrics
- Occupancy rates
- Distinguish from traditional retail $/SF metrics
