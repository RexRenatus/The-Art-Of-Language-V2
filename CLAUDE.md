# Firecrawl v2 - Project Configuration

## Project Overview

This is a **data-centric knowledge base** containing structured JSON files representing a comprehensive software engineering curriculum. The data was collected via web scraping (Firecrawl) and organized into a hierarchical structure.

## Data Structure

```
Data/
├── Raw/                              # Active data files (numbered for learning order)
│   ├── 001_cybersecurity/            # 19 domains, 145 subdomains
│   ├── 002_network-engineering/      # 16 domains, 140 subdomains
│   └── 003_software-engineering/     # 12 domains, 261 subdomains
└── Backup/                           # Timestamped backups
    └── pre-reorganization-20251230/  # Original structure backup
```

**Statistics:**
- 3 categories, 47 domains, ~546 subdomains
- 655 JSON files
- ~23 MB total data

**Categories (Learning Order):**

### 001_cybersecurity (19 domains)
| # | Domain |
|---|--------|
| 001 | security-and-risk-management |
| 002 | security-architecture-and-engineering |
| 003 | asset-security |
| 004 | cryptography |
| 005 | identity-and-access-management-iam |
| 006 | communication-and-network-security |
| 007 | security-operations |
| 008 | security-monitoring-and-analytics |
| 009 | software-development-security |
| 010 | application-security |
| 011 | cloud-security |
| 012 | security-assessment-and-testing |
| 013 | penetration-testing-and-ethical-hacking |
| 014 | threat-intelligence-and-hunting |
| 015 | incident-response-and-forensics |
| 016 | privacy-and-data-protection |
| 017 | operational-technology-ot-and-ics-security |
| 018 | emerging-technologies-and-trends |
| 019 | advanced-security-architecture-and-design-patterns |

### 002_network-engineering (16 domains)
| # | Domain |
|---|--------|
| 001 | network-fundamentals |
| 002 | physical-layer-and-cabling |
| 003 | ethernet-switching-and-vlans |
| 004 | ip-addressing-and-subnetting |
| 005 | ip-routing-fundamentals |
| 006 | ospf-open-shortest-path-first |
| 007 | eigrp-enhanced-interior-gateway-routing-protocol |
| 008 | bgp-border-gateway-protocol |
| 009 | ip-multicast |
| 010 | network-services-and-infrastructure |
| 011 | wan-technologies-and-mpls |
| 012 | wireless-networking |
| 013 | network-security |
| 014 | data-center-networking |
| 015 | software-defined-networking-and-automation |
| 016 | network-troubleshooting-and-management |

### 003_software-engineering (12 domains)
| # | Domain |
|---|--------|
| 001 | foundational-computer-science |
| 002 | system-fundamentals |
| 003 | core-software-engineering |
| 004 | web-development |
| 005 | mobile-application-development |
| 006 | data-science-and-analytics |
| 007 | artificial-intelligence-and-machine-learning |
| 008 | cloud-engineering-and-infrastructure |
| 009 | cybersecurity-and-information-security |
| 010 | game-development |
| 011 | specialized-domains-and-emerging-technologies |
| 012 | project-management-and-professional-practice |

---

## AI Team Configuration

*Last updated: 2025-12-30*

### Recommended Specialists

| Task | Agent | When to Use |
|------|-------|-------------|
| **Data Analysis** | `ml-data-expert` | Analyzing JSON data, computing statistics, finding patterns, data transformations |
| **Web Scraping** | `Python Web Scraping Expert` | Adding new data collection capabilities, expanding the curriculum |
| **Python Scripting** | `python-expert` | Data manipulation utilities, batch processing scripts, automation |
| **Documentation** | `documentation-specialist` | Creating README files, schema documentation, usage guides |
| **Code Review** | `code-reviewer` | Reviewing any future code additions to the project |
| **Performance** | `performance-optimizer` | Optimizing data processing pipelines |
| **Orchestration** | `tech-lead-orchestrator` | Coordinating complex multi-step workflows |
| **Exploration** | `code-archaeologist` | Understanding project structure, investigating data patterns |

### Usage Examples

```bash
# Analyze data patterns
@ml-data-expert "Analyze the JSON structure and find common patterns across categories"

# Add new scraping capability
@Python Web Scraping Expert "Create a scraper to collect additional curriculum data"

# Create data processing script
@python-expert "Write a script to merge and deduplicate JSON files"

# Generate documentation
@documentation-specialist "Create a comprehensive README for the data structure"
```

---

## Project Conventions

### JSON Structure
- All curriculum data stored as JSON
- Hierarchical organization: Category > Domain > Subdomain
- Backups stored with timestamps in `Data/Backup/`

### Folder Naming Convention
- All folders use numbered prefixes: `001_`, `002_`, etc.
- Numbers indicate logical learning order (foundational → advanced)
- Format: `###_topic-name` (3-digit prefix, underscore, kebab-case name)

### File Naming
- Use descriptive names matching the curriculum topic
- Maintain consistent capitalization with original sources

### Data Integrity
- Always create backups before bulk modifications
- Validate JSON structure after any automated changes
