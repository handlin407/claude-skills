# Commercial Real Estate Comp Database Skills

Custom Claude skills for managing commercial real estate comp databases in Airtable. These skills automate transaction parsing, address validation, duplicate detection with fuzzy matching, and record creation across three core database types.

## Overview

This repository contains three production-ready Claude skills for CRE data management:

| Skill | Purpose | Duplicate Logic | Status |
|-------|---------|----------------|---------|
| **transaction-comps** | Property sales & acquisitions | Purchaser + Address | ✓ v1.0.0 |
| **lease-comps** | Tenant lease agreements | Tenant + Address | ✓ v1.1.0 |
| **tenant-sales** | Retailer revenue data | Tenant + Address + Year | ✓ v1.2.0 |

## Key Features

### Intelligent Workflow
**5-Step Process:** Parse → Validate → Format → Check Duplicates → Create
- Automatic address validation with web search for missing ZIP codes
- Comprehensive fuzzy address matching (handles St/Street, N/North, etc.)
- Smart duplicate detection with user override options
- Formatted output with proper currency, measurements, and notes structure

### Market Coverage
**14 Major US Markets** with 55+ predefined submarkets:
- **South Florida** (11): Brickell, Miami Beach, Design District, Wynwood, Coconut Grove, Aventura, Bal Harbour, Palm Beach, Worldcenter, Naples, Las Olas
- **Chicago** (8): Gold Coast, Fulton Market / West Loop, River North, Lincoln Park, Wicker Park / Bucktown, Loop, Michigan Ave, Southport
- **New York** (3): Soho, Madison / 5th Ave, Williamsburg
- **Boston** (5): Back Bay, Seaport, Southie, Cambridge, Brookline, Wellesley
- **San Francisco** (4): Union Square, Hayes Valley, Fillmore St, Chestnut / Marina
- **Los Angeles** (8): Abbot Kinney, West Hollywood, Beverly Hills / Rodeo Drive, Santa Monica, Silverlake, Pasadena, Manhattan Beach, Highland Park
- **Plus:** Austin, Atlanta, Charlotte, Houston, Orlando, Dallas, Washington DC

Custom submarkets accepted for properties outside predefined areas.

### Data Quality Controls
- **Address Normalization**: Comprehensive fuzzy matching prevents duplicates
- **Format Standards**: Consistent currency ($XX,XXX,XXX), size (X,XXX SF), and notes formatting
- **Validation Protocol**: Address verification with override capability
- **Error Handling**: Clear messaging for missing fields or validation issues

## Installation

### Quick Start (Recommended)

1. **Download** the appropriate `.skill` file(s) from [Releases](https://github.com/handlin407/claude-compdatabase-skills/releases)
2. **Upload** to Claude.ai:
   - Go to Settings → Skills
   - Click "Upload Skill"
   - Select the `.skill` file
3. **Use** in any conversation - skills trigger automatically when relevant data is provided

### Manual Installation

```bash
git clone https://github.com/handlin407/claude-compdatabase-skills.git
cd claude-compdatabase-skills
# Copy desired skill folder to your Claude skills directory
```

## Usage Examples

### Transaction Comps

**Simple Transaction:**
```
Blackstone acquired 450 Park Avenue in Manhattan for $500M in Q4 2023. 
The building is a 600,000 SF office tower.
```

**Portfolio Deal:**
```
Brookfield purchased a 3-property South Florida retail portfolio for $125M in 2024:
1) 500 Lincoln Road, Miami Beach - 20,000 SF
2) 200 Las Olas Blvd, Fort Lauderdale - 35,000 SF
3) 300 Miracle Mile, Coral Gables - 18,000 SF
```

### Lease Comps

**Retail Lease:**
```
Nike signed a lease at 100 Lincoln Road, Miami Beach for 5,000 SF at $150/SF/year in Q3 2024.
10-year term with 5-year renewal option.
```

**Office Lease with Complex Terms:**
```
Microsoft leased 50,000 SF at 1000 Brickell Avenue for $75/SF in Q1 2024.
20-year term, $2.5M TI allowance, 6 months free rent, 3% percentage rent over $10M threshold.
```

### Tenant Sales

**Retail Performance:**
```
Apple Store at 100 Lincoln Road, Miami Beach generated $18.5M in 2024.
Store is 12,000 SF flagship location ($1,542/SF).
```

**Restaurant with Metrics:**
```
Carbone at 49 Collins Avenue did $8.2M in 2023.
4,500 SF unit, $180/SF rent, 9.9% occupancy cost, $125 average check, 180 covers/night.
```

## Requirements

- **Claude.ai** with Skills feature enabled
- **Airtable MCP** server configured with access to:
  - Transaction Comps-HD: `appgcNuJlSybJQ0Lf / tblVWAXsv9KxphqdC`
  - Lease Comps-HD: `apptqaigvuDe1izN6 / tblbIOTTyKa0vJSdU`
  - Tenant Sales-HD: `app53DekPcYwDuV3E / tblahbVKhP8HFx7YQ`

## Repository Structure

```
claude-compdatabase-skills/
├── transaction-comps/
│   ├── SKILL.md                          # Core skill instructions
│   └── examples/formatted-records.md     # Reference examples
├── lease-comps/
│   ├── SKILL.md
│   └── examples/formatted-records.md
├── tenant-sales/
│   ├── SKILL.md
│   └── examples/formatted-records.md
├── test-files/
│   ├── transaction-comps-TEST-SUITE.md   # 12 test cases
│   ├── lease-comps-TEST-SUITE.md         # 12 test cases
│   └── tenant-sales-TEST-SUITE.md        # 12 test cases
└── README.md
```

## Testing

Each skill includes a comprehensive test suite with 12 test cases covering:
- Basic workflow validation
- Complex multi-property/multi-unit scenarios
- Missing field error handling
- Exact duplicate detection (fuzzy matching)
- Address validation protocols
- Custom submarket handling
- Large transaction formatting
- And more...

Test files use `[TEST]` markers for easy cleanup. See `test-files/` directory for complete test suites.

## Technical Details

### Fuzzy Address Matching

Comprehensive normalization prevents duplicate entries:
- **Street suffixes**: St/Street, Ave/Avenue, Blvd/Boulevard, Rd/Road, Dr/Drive, Ln/Lane, Ct/Court, Pl/Place, Pkwy/Parkway
- **Directional prefixes**: N/North, S/South, E/East, W/West, NE/Northeast, NW/Northwest, SE/Southeast, SW/Southwest
- **Unit designations**: Ste/Suite, Apt/Apartment, #/Unit, Fl/Floor
- **Case-insensitive** with extra space removal

### Data Formatting Standards

**Currency:** `$XX,XXX,XXX` or `$XX.XM` (with $ and commas/suffix)  
**Size:** `X,XXX SF` (with commas and uppercase SF)  
**Dates:** `Q1 2024`, `2024`, or specific dates  
**Notes:** Semicolon-separated with line breaks for major sections

### Duplicate Detection Logic

| Skill | Fields Checked | Match Criteria |
|-------|---------------|----------------|
| Transaction Comps | Purchaser + Address | Both must match (fuzzy address) |
| Lease Comps | Tenant + Address | Both must match (fuzzy address) |
| Tenant Sales | Tenant + Address + Year | All three must match (fuzzy address) |

## Development

### Building New Skills

Follow the established pattern:
1. Define YAML frontmatter with metadata
2. Document database reference (baseId, tableId, fields)
3. Implement 5-step workflow
4. Add fuzzy matching for duplicate logic
5. Create comprehensive test suite (12+ cases)
6. Provide formatting examples

### Contributing

Contributions welcome! Please:
1. Follow existing skill structure
2. Include test suite with `[TEST]` markers
3. Add formatting examples
4. Document new submarkets
5. Test thoroughly before PR

## Version History

### v1.2.0 (December 16, 2024)
- ✓ All three skills production-ready
- ✓ Comprehensive test suites (36 total test cases)
- ✓ Clean skill packages (TEST-SUITE.md moved to test-files/)
- ✓ 14 markets, 55+ submarkets supported
- ✓ Fuzzy address matching validated
- ✓ Package sizes optimized (7-8KB each)

### v1.0.0 (December 16, 2024)
- ✓ Initial release: transaction-comps skill
- ✓ 5-step workflow implemented
- ✓ Fuzzy address matching engine
- ✓ Test suite framework established

## License

MIT License - See LICENSE file for details.

## Support

For issues or questions:
1. Check [test-files/](test-files/) for comprehensive test scenarios
2. Review [examples/](transaction-comps/examples/) for formatting guidance
3. Open an [issue](https://github.com/handlin407/claude-compdatabase-skills/issues)
4. Reference individual SKILL.md files for detailed documentation

## Author

**Handlin** - Commercial Real Estate Analyst  
Specializing in South Florida markets with comprehensive CRE data management expertise.

---

**Note:** These skills are designed for professional CRE data management. Address validation and duplicate detection features ensure data integrity across growing databases tracking thousands of transactions, leases, and sales records.