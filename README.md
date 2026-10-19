Description of my first coding project called "Mini ERP"

# Mini ERP --- Consolidated Requirements

## 1. Purpose and Scope

The application is intended to be a lightweight, bilingual inventory and
sales management system for a single user.

The company is based in Germany and sells household goods and other
products to customers in Vietnam. A freight forwarder manages export and
transportation from the forwarder's depot in Germany to the company's
own interim warehouse in Vietnam. Goods are then delivered locally to
multiple customers in Vietnam.

The application is **not intended to be a full customs or
export-compliance system**.

## 2. Product Database

### Product categories

-   Household goods
-   Cosmetics
-   Toys
-   Food
-   Electronics
-   Clothing
-   Other

### Product fields

-   Product ID
-   Product name in German and Vietnamese
-   Category
-   Supplier
-   Supplier SKU
-   Internal SKU
-   Stock quantity
-   Minimum stock level
-   Unit of measure
-   Weight, ideally net and gross weight
-   Packaging information
-   Purchase price in EUR
-   Sales price in VND
-   Sales price in EUR as a reference equivalent
-   Exchange rate used
-   Country of origin (optional)
-   Active/inactive status

The product database must support Excel import and export. Migration of
existing product data from the current commercial mini ERP is a future
requirement; the export format and field mapping still need to be
confirmed.

## 3. Inventory and Warehouses

The system must track inventory in at least two locations: - Source
warehouse in Germany - Company-owned interim warehouse in Vietnam

An inventory-movement ledger must record: - Goods receipts - Stock
adjustments - Stock deductions when a delivery is released - Subsequent
receipts into the Vietnam warehouse

Stock must be deducted when a delivery is released, not whenever a
delivery record is merely saved. The system must prevent duplicate stock
movements, for example if a release action is submitted more than once.

## 4. Customer Database

The system must support multiple customers in Vietnam, each with
potentially different billing and delivery addresses.

### Customer fields

-   Customer ID/number
-   Customer or company name
-   Billing address
-   Delivery address
-   Tax ID, where applicable
-   Default payment currency (VND)
-   Payment terms (default: 14 days from invoice date)
-   Email address
-   Phone number
-   Preferred document language (German or Vietnamese)
-   Active/inactive status
-   Internal notes

### Derived customer metrics

-   Date of last order
-   Number of orders year to date
-   Revenue year to date in VND
-   Total number of orders
-   Total revenue in VND

## 5. Deliveries, Delivery Notes, and Packages

A delivery should be managed through a central workflow.

Each delivery must have: - Unique delivery ID and delivery-note number -
Delivery date - Customer and delivery address - Status - Document
language and currency - Optional freight forwarder/depot details and
forwarder reference - Release date and handover date

### Delivery line items

Each line item should include: - Product - SKU - Snapshot of the product
name at the time of the transaction - Quantity and unit of measure -
Price - Optional discount - Optional note

### Packages and packing lists

The system must support multiple packages per delivery. Each package may
include: - Package number - Package type - Assigned products and
quantities - Optional dimensions - Optional net and gross weight

The quantities in the packing list must reconcile with the delivery
line-item quantities.

## 6. Invoices

An invoice must be generated directly from the delivery and packing-list
workflow.

For the initial MVP, **one delivery corresponds to one invoice**.

Each invoice must include: - Invoice number and date - Recipient and
address - Line items - Saved quantities and prices - Currency (VND by
default) - Payment terms inherited from the customer (14 days by
default) - Exchange rate used - Subtotal, discounts, tax, and total -
PDF or Excel output - Immutable historical record

The user must be able to choose whether Vietnamese VAT is displayed on
the invoice. This setting must control the invoice's calculation and
presentation logic. However, the application must not imply that VAT may
legally be omitted merely because a user interface option is switched
off. The applicable tax treatment, rates, and mandatory invoice
information still need to be confirmed.

## 7. Payment Tracking

The system must support the following payment statuses: - Unpaid -
Partially paid - Paid

For each invoice, store: - Invoice total - Amount paid - Outstanding
balance - Invoice date - Due date - Payment status - Optional payment
date and note

Multiple partial payments must be supported. The due date is calculated
as the invoice date plus the customer's payment term. No bank
integration is required for the initial version.

## 8. Foreign Exchange (EUR/VND)

The system should retrieve current EUR/VND exchange rates from a
suitable source and allow manual entry or override.

Requirements: - Make the exchange-rate direction explicit, e.g. **1 EUR
= 28,000 VND** - Store the rate, source, and date - Save the exact rate
used for each calculation and invoice - Never change historical invoices
when the current exchange rate is updated

Purchases are always recorded in EUR; sales prices are in VND. The
product database may also include a reference sales price in EUR and the
exchange rate applied.

**Open decision:** Should the VND sales price be authoritative and
entered manually, or should it be calculated from an EUR reference
price?

## 9. Reports and Analytics

The system should provide: - Monthly revenue in VND - Revenue by
customer - Revenue by product - Revenue by product category - Number of
orders and deliveries - Units sold - Outstanding and overdue
receivables - Year-to-date and all-time revenue

Cost of goods sold (COGS) and gross profit are optional and should only
be included if a reliable historical cost basis is available.

Credit notes and cancellations must be handled. If multiple currencies
are supported, the reporting currency-conversion policy must be defined.

## 10. Company Settings and Documents

Company settings should include: - Company name - Company address -
Optional VAT ID - Additional contact details, if required

No logo is required. Documents should display the company name centered
at the top.

The system must support PDF and Excel output. The document language
(German or Vietnamese) must be selectable independently of the
user-interface language.

Documents must include, as applicable: - Unique sequential document
numbers - Customer and address details - Quantities - Currency - Dates -
Payment terms and due date - Relevant tax fields

## 11. Platform and Technical Architecture

The application should be a responsive web app or Progressive Web App
(PWA), accessible from: - MacBook - Windows PC - Mobile devices

The frontend may use HTML/CSS/JavaScript or TypeScript.

Possible database options: - SQLite for a simple local prototype -
PostgreSQL for a hosted application

The recommended direction is a hosted web application with a backend,
database, backups, and access protection. A single standalone HTML file
is not sufficient for reliable inventory and invoice management.

PDF and Excel documents must be generated from persisted application
data. Exchange-rate APIs should be accessed through the backend.

## 12. Proposed Data Model

The proposed data model includes: - Products - Product translations -
Customers - Deliveries - Delivery line items - Packages - Package line
items - Invoices - Invoice line items - Payments - Warehouses - Stock
movements - Exchange rates - Company settings

The existing Excel data-model template currently contains only an
initial subset of these schemas. Payments, warehouses, exchange rates,
product translations, and company settings still need to be added or
completed.

## 13. Initial Non-Goals

The initial version does not need: - Full customs declarations - Bank
integration or automatic payment reconciliation - Multi-user
permissions - Complex transport planning or freight-forwarder
integration - Full accounting functionality

## 14. Open Decisions and Items to Confirm

The following points still need clarification: 1. Sales-price logic:
manually maintained VND price or calculated from an EUR reference price?
2. Preferred EUR/VND exchange-rate source 3. Applicable VAT treatment,
rate, and legally required invoice information 4. How receipt of goods
at the Vietnam warehouse is confirmed and recorded 5. Inventory
valuation method, if gross profit is to be calculated 6. How
cancellations and credit notes should be handled 7. Hosting, access
protection, and backup requirements 8. Export format and field mapping
for migrating product data from the existing commercial mini ERP 9.
Currency-conversion policy for reports if multiple currencies are used

## 15. Suggested Development Phases

### Phase 1 --- Foundation

-   Products
-   Customers
-   Company settings
-   German and Vietnamese language support
-   Warehouses

### Phase 2 --- Operational Workflow

-   Deliveries
-   Packages and packing lists
-   Delivery documents
-   Stock movements

### Phase 3 --- Billing and Payments

-   Invoices
-   Exchange rates
-   VAT logic
-   Partial-payment tracking

### Phase 4 --- Reporting and Data Migration

-   Reports and exports
-   Product-data import
-   Migration from the existing system
