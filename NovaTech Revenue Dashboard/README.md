#NovaTech Revenue Dashboard
An interactive Amazon QuickSight dashboard that unifies CRM, marketing, and customer support data into a single, decision-ready view for the revenue team — built to replace a Monday-morning ritual of copy-pasting numbers from three disconnected tools into a slide deck.

The Problem
NovaTech's revenue data lived in three separate systems that couldn't talk to each other:
A CRM with every sales deal ever worked — accounts, products, outcomes, and loss reasons
A marketing platform with campaign performance — channels, leads, and attributed revenue
A support system with every customer ticket — severity, resolution time, and sentiment
Nobody could trace a marketing campaign through to a closed deal, or tell whether a high-value account was quietly becoming a high-maintenance one. This dashboard connects all three, in one place, for the whole revenue team.

What's Inside
A three-sheet QuickSight dashboard, each sheet answering one core business question:

Sheet	Answers
Marketing Funnel	Which channels and campaigns actually generate leads that convert? Which campaigns are paying for themselves?
Sales Pipeline	How's our deal flow — win rates, loss reasons, revenue by product and segment, days to close?
Customer Health	Which accounts are becoming support-heavy, and are any of them also our highest-value accounts?
Data Architecture

Three source datasets connect through account_id, a shared identifier across all three systems:

Dataset	File	Rows	Columns
CRM Deals	novatech_crm_deals.csv	499	20
Marketing Campaigns	novatech_marketing_campaigns.csv	2,240	20
Support Tickets	novatech_support_tickets.csv	3,000	20

account_id is unique in CRM (85 accounts) but not in Marketing or Support, where a single account can generate many leads and many tickets. Joining all three directly on that key would duplicate every matching row, inflating any revenue or deal count computed afterward. To avoid that, Marketing and Support were each pre-aggregated to one summary row per account before joining — CRM as the anchor table, two left joins on account_id chaining in the pre-aggregated Marketing summary and then the pre-aggregated Support summary. This produced a fourth, unified dataset used specifically where the dashboard needs to compare activity across systems (e.g., support ticket volume against deal value for the same account), while the Marketing Funnel and Sales Pipeline sheets run directly off their own native source data.

Datasets saved to SPICE (QuickSight's in-memory data store):

novatech_crm_deals (source)
novatech_marketing_campaigns (source)
novatech_support_tickets (source)
novatech_unified_account_view (joined — CRM ← Marketing summary ← Support summary)
Key Calculated Fields

A handful of derived metrics power the KPIs and charts across all three sheets, including:

Days to Close — sales cycle length, from deal creation to close
Overall Sales Win Rate % — won deals as a share of all closed opportunities
Response Rate % and Campaign ROI — marketing effectiveness by campaign
Ticket Resolution Time — support responsiveness, broken down by priority
Is At-Risk Account — flags accounts with high recent ticket volume, negative sentiment, and high deal value, computed on the unified dataset
Interactivity
Filter controls on the Marketing Funnel and Sales Pipeline sheets (campaign, channel, date range, segment, region, product, deal stage, and more)
A one-click filter action on the Customer Health sheet — selecting a bar updates related charts on the same sheet
A cross-sheet navigation action — clicking an at-risk account jumps to its full deal history on the Sales Pipeline sheet
Natural-language querying via a configured QuickSight Topic, letting stakeholders ask plain-English questions directly against the data
Repository Contents
├── README.md                          # this file
├── data/
│   ├── novatech_crm_deals.csv
│   ├── novatech_marketing_campaigns.csv
│   └── novatech_support_tickets.csv
├── docs/
│   ├── data-dictionary.md             # field-level documentation for all three source datasets
│   ├── build-plan.md                  # full data prep, join, and dashboard build plan
│   └── report.pdf                     # written report for VP Sarah Chen — data strategy, design rationale, key insights
├── screenshots/
│   ├── verification-log.png           # data quality checks against Quick Chat
│   ├── spice-import.png               # dataset import confirmation (row/column counts)
│   ├── join-diagram.png               # unified dataset join configuration
│   ├── marketing-funnel.png
│   ├── sales-pipeline.png
│   ├── customer-health.png
│   ├── topic-setup.png                # Topic configuration
│   └── quick-chat-before-after.png    # baseline vs. post-Topic Q&A comparison
└── dashboard-export.pdf               # exported PDF of the full published dashboard, all three sheets

Built With
Amazon QuickSight (Enterprise Edition) — dashboard, SPICE datasets, and Topics/Quick Chat for natural-language querying
Source data: three CSV datasets (novatech_crm_deals.csv, novatech_marketing_campaigns.csv, novatech_support_tickets.csv)
Screenshots

Author

Built by stargazingpnw as a data analytics project exploring end-to-end BI development: data quality verification, no-code data preparation and joins, dashboard design, and AI-assisted data exploration in Amazon QuickSight.
