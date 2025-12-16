# Claude Skills for Commercial Real Estate Database Management

Custom Claude skills for managing commercial real estate data in Airtable databases. These skills automate the workflow of parsing transaction information, validating addresses, checking for duplicates with fuzzy matching, and creating properly formatted records.

## Skills

### 1. transaction-comps

**Purpose:** Manages commercial real estate property sales and acquisition transactions.

**Use when:** User provides property purchase, sale, or acquisition information that includes:
- Purchaser/buyer name
- Property address
- Sale price
- Building size
- Transaction date

**Key Features:**
- 5-step workflow: Parse → Validate → Format → Check duplicates → Draft → Create
- Comprehensive fuzzy address matching (handles St/Street, N/North, Ave/Avenue variations)
- Web search for address validation (finds missing ZIP codes)
- Predefined submarkets for 14 major US markets
- Test record markers for easy cleanup

**Database:** Airtable Transaction Comps-HD table

**Status:** ✓ Complete and tested (Test Case 1 passed)

### 2. lease-comps (Coming Soon)

**Purpose:** Manages commercial real estate tenant lease agreements.

**Status:** Planned

### 3. tenant-sales (Coming Soon)

**Purpose:** Manages tenant sales revenue data.

**Status:** Planned

## Installation

### Option 1: Upload .skill Package (Recommended)

1. Download the `.skill` file from the releases
2. In Claude.ai, go to Settings → Skills
3. Click "Upload Skill" and select the `.skill` file
4. The skill will be available in all your conversations

### Option 2: Manual Installation

1. Clone this repository
2. Copy the skill folder to your Claude skills directory
3. Restart Claude or reload skills

## Usage

### transaction-comps

**Simple Example:**
```
Blackstone acquired 450 Park Avenue in Manhattan for $500M in Q4 2023. 
The building is 600,000 SF office tower.
```

The skill will:
1. Parse the transaction details
2. Validate the address (add ZIP code if missing)
3. Format all fields properly ($500,000,000, 600,000 SF)
4. Check for duplicates using fuzzy matching
5. Draft a record for your approval
6. Create the record in Airtable once approved

**Complex Example:**
```
Starwood Capital purchased Lincoln Road retail building in Miami Beach for $210M, Q1 2024. 
Property is 85,000 SF flagship retail with tenants including Apple and Tesla. 
Deal was 65% LTV with Wells Fargo providing senior debt. 
In-place NOI is $14.7M representing 7.0% cap rate.
```

The skill will capture all details in properly formatted Notes field.

## Testing

Each skill includes a comprehensive test suite with:
- 12+ test cases covering all scenarios
- Test record markers (`[TEST]` prefix) for easy cleanup
- Pre-test and post-test checklists
- Success criteria validation

See individual skill folders for detailed test suites.

## Supported Markets

The skills include predefined submarkets for:
- **Chicago** (8 submarkets)
- **South Florida** (11 submarkets)
- **New York** (3 submarkets)
- **Boston** (5 submarkets)
- **San Francisco** (4 submarkets)
- **Los Angeles** (8 submarkets)
- **Austin** (3 submarkets)
- **Atlanta** (3 submarkets)
- **Charlotte** (1 submarket)
- **Houston** (1 submarket)
- **Orlando** (4 submarkets)
- **Dallas** (1 submarket)
- **Washington DC** (3 submarkets)

Custom submarkets can be specified for properties outside these predefined areas.

## Requirements

- Claude with Skills feature enabled
- Airtable MCP server configured
- Access to the relevant Airtable bases:
  - Transaction Comps-HD: `appgcNuJlSybJQ0Lf`
  - Lease Comps-HD: (coming soon)
  - Tenant Sales-HD: (coming soon)

## File Structure

```
transaction-comps/
├── SKILL.md                    # Main skill instructions (350 lines)
├── TEST-SUITE.md                # Comprehensive test suite (12 test cases)
└── examples/
    └── formatted-records.md     # 5 detailed formatting examples
```

## Features

### Fuzzy Address Matching

Comprehensive address normalization prevents duplicate entries:
- Street suffix variations (St/Street, Ave/Avenue, Blvd/Boulevard)
- Directional prefix variations (N/North, S/South, etc.)
- Unit designation variations (Ste/Suite, Apt/Unit)
- Case-insensitive matching
- Extra space removal

### Address Validation

Automatically finds missing ZIP codes using web search:
- Validates every address before record creation
- Override protocol available if needed
- Documents validation status in notes

### Data Formatting Standards

**Price:** `$XX,XXX,XXX` or `$XX.XM` (always with $ and commas/suffix)

**Size:** `X,XXX SF` (always with commas and uppercase SF)

**Notes:** Semicolon-separated points with line breaks for major sections

### Error Handling

- Missing required fields → STOP and list what's needed
- Address validation failure → STOP and request complete address
- Duplicate detected → STOP and present options (Update/Create/Skip)
- Airtable API errors → Show error and ask to retry

## Development

### Building New Skills

Follow the same pattern:
1. Define YAML frontmatter with name and description
2. Document database reference (baseId, tableId, fields)
3. Create step-by-step workflow
4. Implement fuzzy matching for your duplicate logic
5. Add comprehensive test suite
6. Provide formatting examples

### Contributing

Contributions welcome! Please:
1. Follow existing skill structure
2. Include comprehensive test suite
3. Add formatting examples
4. Document submarket additions
5. Test thoroughly before submitting PR

## License

MIT License - See LICENSE file for details

## Author

Handlin - Commercial Real Estate Analyst

## Changelog

### v1.0.0 (December 16, 2024)
- ✓ transaction-comps skill complete
- ✓ 5-step workflow implemented
- ✓ Fuzzy address matching validated
- ✓ Test suite created (12 test cases)
- ✓ Test Case 1 passed successfully
- ✓ Formatting examples documented
- □ lease-comps skill (planned)
- □ tenant-sales skill (planned)

## Support

For issues or questions:
1. Check the TEST-SUITE.md for common scenarios
2. Review examples in formatted-records.md
3. Open an issue on GitHub
4. Reference the relevant SKILL.md documentation