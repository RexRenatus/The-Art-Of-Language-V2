# Firecrawl v2 - Project Configuration

## Project Overview

This is a **data-centric knowledge base** containing structured JSON files representing a comprehensive software engineering curriculum. The data was collected via web scraping (Firecrawl) and organized into a hierarchical structure.

## Data Structure

```
Data/
├── Raw/                              # Active data files (numbered for learning order)
│   ├── 001_cybersecurity/            # 19 domains, 145 subdomains
│   ├── 002_network-engineering/      # 16 domains, 140 subdomains
│   └── 003_software-engineering/     # 12 domains, 70 subdomains
└── Backup/                           # Timestamped backups
    └── pre-summary-update-20260104/  # Summary update backup
```

**Statistics:**
- 3 categories, 47 domains, 355 subdomains
- 139,403 topics
- ~23 MB total data

**Categories (Learning Order):**

### 001_cybersecurity (19 domains)
| # | Domain |
|---|--------|
| 001 | security-and-risk-management |
| 002 | asset-security |
| 003 | privacy-and-data-protection |
| 004 | identity-and-access-management-iam |
| 005 | cryptography |
| 006 | communication-and-network-security |
| 007 | security-architecture-and-engineering |
| 008 | software-development-security |
| 009 | application-security |
| 010 | cloud-security |
| 011 | operational-technology-ot-and-ics-security |
| 012 | security-operations |
| 013 | security-monitoring-and-analytics |
| 014 | security-assessment-and-testing |
| 015 | incident-response-and-forensics |
| 016 | penetration-testing-and-ethical-hacking |
| 017 | threat-intelligence-and-hunting |
| 018 | advanced-security-architecture-and-design-patterns |
| 019 | emerging-technologies-and-trends |

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
| 010 | wan-technologies-and-mpls |
| 011 | wireless-networking |
| 012 | network-services-and-infrastructure |
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
| 004 | project-management-and-professional-practice |
| 005 | web-development |
| 006 | mobile-application-development |
| 007 | game-development |
| 008 | data-science-and-analytics |
| 009 | artificial-intelligence-and-machine-learning |
| 010 | cloud-engineering-and-infrastructure |
| 011 | cybersecurity-and-information-security |
| 012 | specialized-domains-and-emerging-technologies |

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
