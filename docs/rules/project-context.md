# Project Context

## Project Overview

- **Problem being solved**: Fragmented multi-broker account management across different trading platforms (proprietary trading, forex, cryptocurrency, binary options)
- **Target users**:
  - Retail forex traders (100-1000 users)
  - Prop firm traders participating in challenges and funded accounts (50-500 users)
  - Crypto traders managing multiple exchange accounts (100-1000 users)
  - Professional trading teams with multiple traders (10-50 users)
- **Usage scenarios**:
  - Traders consolidating multiple broker accounts into unified dashboard
  - Prop traders tracking challenge progression and funded account status
  - Multi-asset portfolio monitoring across forex, crypto, and binary options
  - Team managers overseeing trading performance across multiple traders

## Development Structure

- **Team composition**: Solo developer (individual development)
- **Development phase**: Prototype phase (building MVP and core features)

## Business Constraints

1. **Real-time trade data synchronization**: All trade data must synchronize within 2 seconds latency to support live trading decisions
2. **Multi-broker API integration reliability**: System must maintain 99.9% uptime for broker API connections to prevent data loss
3. **Regulatory compliance**: Implement audit trail logging and data retention for financial regulatory requirements
4. **Security enforcement**: Mandatory authentication, authorization, and data encryption for all sensitive trading data and account credentials

## Implementation Principles

### Implementation Policy Characteristics

- **LLM-Driven Implementation**: Claude Code functions as the primary implementer
- **Quality-First Culture**: Prioritize quality over speed
- **YAGNI Principle**: Don't implement until necessary
- **Systematic Design**: Design process through ADR/Design Doc/work plans
