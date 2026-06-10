# E-Commerce-Conversion-Sales-Analytics

### Dim_Date

DateID	Date	Year	Month	MonthNum	Quarter	CalendarQuarter	WeekNum_ISO	DayOfWeek	DayName	IsWeekend	IsUKBankHoliday	BankHolidayName	IsSchoolHoliday	Season	RetailSeason	FiscalYear	FiscalQuarter	FiscalWeek	IsPeakTradingPeriod	PeakLabel	MonthYear	DayOfYear

###Dim_Geography

GeographyID	Postcode	PostcodeArea	TownCity	County	ONS_Region	ONS_RegionCode	SubRegion	Latitude	Longitude	UrbanRural	PopDensityPerKm2	MedianHouseholdIncome_GBP	IsLondon	IsDevolved

### Dim_Customer

CustomerID	FirstName	LastName	Email	Gender	AgeGroup	GeographyID	Postcode	Town	ONS_Region	AcquisitionChannel	AcquisitionDate	TenureMonths	IsNewCustomer	NewOrReturning	LoyaltyTier	EmailOptIn	SmsOptIn	PreferredDevice	LifetimeOrders

### Dim_Product

ProductID	SKU	ProductName	Category	SubCategory	Brand	ListPrice_GBP	CostPrice_GBP	GrossMarginPct	VATRate	IsActive	Supplier	OriginCountry	WeightKg	IsSeasonal	LaunchDate

### Dim_Channel

ChannelID	ChannelName	ChannelType	Platform	Tool	AvgCPC_GBP	MonthlySpend_GBP	EstMonthlyImpressions	ConversionQuality	Attribution	Targeting

### Dim_ProductTag

TagID	TagName	TagDescription

### Bridge_ProductTag

BridgeID	ProductID	TagID	AssignedDate

### Bridge_CustomerSegment

BridgeID	CustomerID	SegmentID	AssignedDate	IsPrimarySegment

### Dim_CustomerSegment

SegmentID	SegmentName	Description	CriteriaRule

### Dim_Warehouse 

WarehouseID	WarehouseCode	WarehouseName	Location	Region	ONS_RegionCode	GeographyID	IsActive	CapacitySqFt	OpenedDate	HasSameDayCapability

### Fact_Orders

OrderID	OrderDateID	OrderDate	ShippedDateID	ShippedDate	CustomerID	CustomerGeographyID	ProductID	ChannelID	WarehouseID	WarehouseRegionGeoID	Quantity	UnitPrice_GBP	DiscountPct	DiscountAmount_GBP	NetRevenue_GBP	VATAmount_GBP	GrossRevenue_GBP	CostOfGoods_GBP	GrossProfit_GBP	ShippingCost_GBP	ShippingMethod	PaymentMethod	OrderStatus	IsRefunded	RefundAmount_GBP	DeliveryDays	CustomerRating	IsRepeatCustomer	PromotionCode

### Fact_Sessions

SessionID	SessionDateID	SessionDate	ConversionDateID	ConversionDate	CustomerID	GeographyID	ChannelID	DeviceType	Browser	OperatingSystem	LandingPage	LandingPageCategory	ExitPage	PageViews	TimeOnSiteSecs	Bounced	AddedToCart	ReachedCheckout	Converted	OrderID	SessionSource	UTMCampaign	UTMMedium	IsNewVisitor	CartValue_GBP

### Fact_Returns

ReturnID	OriginalOrderID	ReturnDateID	ReturnDate	CustomerID	ProductID	QuantityReturned	ReturnReason	RefundValue_GBP	ReturnStatus	ProcessingDays	WarehouseReceivedID	IsWarrantyReturn


# UK E-Commerce Conversion & Sales Analytics

## Project Overview

This Power BI report was developed as part of the **UK E-Commerce Conversion & Sales Analytics Challenge**. The objective was to analyze the complete customer journey from website sessions and marketing acquisition through order conversion, product sales, customer segmentation, fulfillment operations, and returns management.

The dataset covers FY2024-25 (April 2024 – March 2025) and includes advanced Power BI modeling concepts such as:

* Star Schema Data Modeling
* USERELATIONSHIP()
* Many-to-Many Relationships
* Bridge Tables
* Fiscal Year Time Intelligence
* Dynamic Field Parameters
* Product & Customer Segmentation
* Marketing Attribution Analysis
* Geographic Intelligence
* Conversion Funnel Analytics

---

## Data Model

The model follows a star schema architecture with multiple fact tables and shared dimensions.

### Fact Tables

* Fact_Orders
* Fact_Sessions
* Fact_Returns

### Dimension Tables

* Dim_Date
* Dim_Product
* Dim_Customer
* Dim_Channel
* Dim_Geography
* Dim_Warehouse
* Dim_ProductTag
* Dim_CustomerSegment

### Bridge Tables

* Bridge_ProductTag
* Bridge_CustomerSegment

### Advanced Modeling Features

#### Role-Playing Date Dimension

The model includes multiple date relationships:

* Order Date (Active)
* Shipped Date (Inactive)
* Session Date (Active)
* Conversion Date (Inactive)
* Return Date (Inactive)

Inactive relationships are activated using:

```DAX
USERELATIONSHIP()
```

#### Many-to-Many Relationships

Products can belong to multiple tags:

* Premium
* Bestseller
* Seasonal
* Eco-Friendly
* UK Made

Customers can belong to multiple segments:

* High Value
* Loyalty Member
* Deal Seeker
* Lapsed
* Gift Buyer

Bridge tables ensure accurate filtering and aggregation without double-counting.

---

# Page 1: Overview & Conversion Summary

## Purpose

The Overview & Conversion Summary page provides executives with a high-level view of business performance, conversion efficiency, profitability, and revenue growth.

This page combines financial KPIs, conversion metrics, fiscal-year analysis, and regional performance into a single executive dashboard.

---

## Key Performance Indicators

### Total Revenue (£)

Calculates net revenue generated after discounts.

```DAX
Total Revenue =
SUMX(
    Fact_Orders,
    Fact_Orders[Quantity]
        * Fact_Orders[UnitPrice_GBP]
        * (1 - Fact_Orders[DiscountPct])
)
```

---

### Total Cost (£)

Calculates total product cost using product cost prices from the Product dimension.

```DAX
Total Cost =
SUMX(
    Fact_Orders,
    RELATED(Dim_Product[CostPrice_GBP])
        * Fact_Orders[Quantity]
)
```

---

### Total Gross Profit (£)

```DAX
Total Gross Profit =
[Total Revenue] - [Total Cost]
```

---

### Gross Margin %

```DAX
Gross Margin % =
DIVIDE(
    [Total Gross Profit],
    [Total Revenue]
)
```

---

### Total Orders

```DAX
Total Orders =
DISTINCTCOUNT(Fact_Orders[OrderID])
```

---

### Total Customers

```DAX
Total Customers =
DISTINCTCOUNT(Fact_Orders[CustomerID])
```

---

### Average Order Value (AOV)

```DAX
AOV =
DIVIDE(
    [Total Revenue],
    [Total Orders]
)
```

---

### Conversion Rate %

```DAX
Conversion Rate % =
DIVIDE(
    [Converted Sessions],
    [Total Sessions]
)
```

---

## Revenue Analysis

### Revenue by Order Date

Uses the active Order Date relationship.

```DAX
Revenue (Order Date) =
[Total Revenue]
```

---

### Revenue by Shipped Date

Activates the inactive Shipped Date relationship.

```DAX
Revenue (Shipped Date) =
CALCULATE(
    [Total Revenue],
    USERELATIONSHIP(
        Fact_Orders[ShippedDateID],
        Dim_Date[DateID]
    )
)
```

This measure enables comparison between customer purchasing behavior and actual fulfillment timing.

---

## Fiscal Year Analysis

The dataset follows the UK fiscal year:

* FYQ1: April – June
* FYQ2: July – September
* FYQ3: October – December
* FYQ4: January – March

### Fiscal Year-To-Date Revenue

```DAX
FYTD Revenue =
TOTALYTD(
    [Total Revenue],
    Dim_Date[Date],
    "31/03"
)
```

---

## Running Total Analysis

### Revenue Running Total

```DAX
Revenue Running Total =
CALCULATE(
    [Total Revenue],
    FILTER(
        ALLSELECTED(Dim_Date[Date]),
        Dim_Date[Date]
            <= MAX(Dim_Date[Date])
    )
)
```

This visual highlights cumulative revenue growth throughout the fiscal year.

---

## Dynamic Sales Breakdown

A Field Parameter was implemented allowing users to dynamically switch between:

* Product Category
* UK Region
* Marketing Channel

This enables flexible business analysis without changing pages.

### Dynamic Title

```DAX
Dynamic Title =
"Revenue by "
&
SELECTEDVALUE(
    'Breakdown Parameter'[Breakdown Parameter]
)
```

---

## Marketing Performance

### Return on Ad Spend (ROAS)

```DAX
ROAS =
DIVIDE(
    [Total Revenue],
    [Marketing Spend]
)
```

### Cost Per Acquisition (CPA)

```DAX
CPA =
DIVIDE(
    [Marketing Spend],
    [Converted Sessions]
)
```

Conditional formatting dynamically highlights channel performance:

* Green: ROAS ≥ 5
* Amber: ROAS 3–5
* Red: ROAS < 3

---

## Visuals Included

### KPI Cards

* Revenue
* Gross Profit
* Gross Margin %
* Orders
* Customers
* Conversion Rate
* Average Order Value

### Trend Analysis

* Revenue by Order Date
* Revenue by Shipped Date
* Fiscal Year Revenue Trend
* Running Total Revenue

### Dynamic Analysis

* Revenue by Product Category
* Revenue by Region
* Revenue by Marketing Channel

### Geographic Intelligence

* Revenue by UK Region
* Customer Distribution
* Conversion Performance by Geography

---

## Key Business Insights

* Identify revenue growth trends throughout the fiscal year.
* Compare order date revenue against shipped date revenue.
* Evaluate overall profitability and margin performance.
* Monitor customer acquisition efficiency.
* Measure conversion performance across marketing channels.
* Analyze geographic sales distribution across the United Kingdom.






# Page 2: Conversion Funnel & Session Analytics

## Purpose

The Conversion Funnel & Session Analytics page focuses on understanding customer behavior throughout the online purchasing journey. This page identifies where visitors abandon the funnel, evaluates marketing channel effectiveness, and measures conversion performance across devices, browsers, and campaigns.

The objective is to help Digital Marketing Teams and E-Commerce Managers optimize customer acquisition, improve website conversion rates, and reduce revenue leakage.

---

## Key Performance Indicators

### Total Sessions

Measures the total number of website visits.

```DAX
Total Sessions =
COUNTROWS(Fact_Sessions)
```

### Conversion Rate %

Measures the percentage of sessions that resulted in a completed purchase.

```DAX
Conversion Rate % =
DIVIDE(
    [Converted Sessions],
    [Total Sessions]
)
```

### Bounce Rate %

Measures the percentage of visitors who left the website without meaningful engagement.

```DAX
Bounce Rate % =
DIVIDE(
    [Bounce Sessions],
    [Total Sessions]
)
```

### Cart Abandonment Rate %

Measures the percentage of customers who added products to the cart but did not complete a purchase.

```DAX
Cart Abandonment Rate % =
DIVIDE(
    [Cart Abandonment Sessions],
    [Added To Cart]
)
```

### Return on Ad Spend (ROAS)

Measures revenue generated for every pound spent on marketing.

```DAX
ROAS =
DIVIDE(
    [Total Revenue],
    [Marketing Spend]
)
```

### Cost Per Acquisition (CPA)

Measures the cost required to acquire a single converted customer.

```DAX
CPA =
DIVIDE(
    [Marketing Spend],
    [Converted Sessions]
)
```

---

## Purchase Completion Analysis

The dataset contains two separate dates:

* Session Start Date
* Purchase Completion Date

A customer may visit the website on one day and return later to complete the purchase.

To analyze completed purchases correctly, an inactive relationship between Fact_Sessions and Dim_Date is activated using USERELATIONSHIP().

```DAX
Completed Purchases =
CALCULATE(
    [Converted Sessions],
    USERELATIONSHIP(
        Fact_Sessions[ConversionDateID],
        Dim_Date[DateID]
    )
)
```

This analysis helps identify successful re-engagement and cart recovery behavior.

---

## Conversion Funnel

The funnel visual tracks customers through the following stages:

1. Website Session
2. Added To Cart
3. Reached Checkout
4. Completed Purchase

The funnel highlights where the highest volume of potential revenue is being lost and helps prioritize optimization efforts.

---

## Marketing Channel Analysis

Marketing performance is analyzed across:

* Google Ads
* Bing Ads
* Meta
* TikTok
* Email Marketing
* Referral
* Organic Search

Metrics include:

* Revenue
* Conversion Rate
* ROAS
* CPA

Conditional formatting is applied dynamically to highlight channel performance:

* Green: ROAS ≥ 5
* Amber: ROAS between 3 and 5
* Red: ROAS < 3

---

## Device & Browser Analysis

Customer behavior is segmented by:

### Device Type

* Desktop
* Mobile
* Tablet

### Browser

* Chrome
* Safari
* Edge
* Firefox

This analysis identifies platform-specific conversion opportunities and user experience issues.

---

## Campaign Performance

UTM campaign tracking enables comparison of:

* Revenue
* Conversion Rate
* Sessions
* Purchases

This analysis helps identify the highest-performing campaigns and optimize future marketing investments.

---

## Visuals Included

### KPI Cards

* Total Sessions
* Conversion Rate %
* Bounce Rate %
* Cart Abandonment Rate %
* ROAS
* CPA

### Funnel Analysis

* Session → Cart → Checkout → Purchase Funnel

### Trend Analysis

* Monthly Purchase Completions
* Conversion Trend

### Marketing Analysis

* ROAS by Channel
* CPA by Channel
* Campaign Performance

### Behavioral Analysis

* Device Performance
* Browser Performance

---

## Key Business Insights

* Identify the largest points of conversion leakage.
* Measure campaign effectiveness across channels.
* Evaluate customer acquisition efficiency.
* Understand user behavior by device and browser.
* Improve remarketing and cart recovery strategies.
* Optimize marketing budget allocation based on ROAS and CPA.

---

# Page 3: Product Performance

## Purpose

The Product Performance page evaluates product profitability, customer value, returns performance, product tags, and customer segments.

This page leverages advanced Power BI modeling techniques including many-to-many relationships, bridge tables, and ranking measures to provide deeper commercial insights.

---

## Product Performance Analysis

Products are evaluated using:

* Revenue
* Gross Profit
* Gross Margin %
* Revenue Rank
* Margin Rank

Revenue and profitability rankings update dynamically based on report filters, ensuring rankings remain relevant within the selected context.

### Revenue Rank

```DAX
Product Revenue Rank =
RANKX(
    ALLSELECTED(Dim_Product[ProductName]),
    [Total Revenue],
    ,
    DESC,
    DENSE
)
```

### Margin Rank

```DAX
Product Margin Rank =
RANKX(
    ALLSELECTED(Dim_Product[ProductName]),
    [Gross Margin %],
    ,
    DESC,
    DENSE
)
```

---

## Product Tag Analysis

Products can belong to multiple tags simultaneously:

* Premium
* Bestseller
* Seasonal
* Eco-Friendly
* UK Made
* New In
* Gift Suitable
* Clearance

The analysis uses the Bridge_ProductTag table to correctly allocate revenue across tags without creating duplicate counts.

Model Structure:

Dim_ProductTag → Bridge_ProductTag → Dim_Product → Fact_Orders

Metrics analyzed:

* Revenue by Tag
* Gross Profit by Tag
* Return Rate by Tag

---

## Customer Segment Analysis

Customers may belong to multiple behavioral segments:

* High Value
* Loyalty Member
* Deal Seeker
* Gift Buyer
* Lapsed Customer

The analysis leverages the Bridge_CustomerSegment table to support many-to-many segmentation.

Model Structure:

Dim_CustomerSegment → Bridge_CustomerSegment → Dim_Customer → Fact_Orders

Metrics analyzed:

* Revenue by Segment
* Gross Profit by Segment
* Orders by Segment

---

## Returns & Refund Analysis

Returns performance is evaluated using:

* Total Returns
* Return Rate %
* Refund Exposure
* Return Reason Analysis

Common return reasons include:

* Faulty Product
* Changed Mind
* Not As Described

This analysis helps identify quality issues, fulfillment challenges, and product risk areas.

---

## Customer Value Analysis

Customer value is calculated at the individual customer level before averaging across groups.

This approach provides a more accurate measure of customer worth than simply dividing revenue by order count.

Metrics include:

### Average Customer Spend

```DAX
Average Customer Spend =
AVERAGEX(
    VALUES(Dim_Customer[CustomerID]),
    CALCULATE([Total Revenue])
)
```

Analysis is performed by:

* Loyalty Tier
* UK Region

---

## Loyalty Tier Analysis

Customers are segmented into:

* Bronze
* Silver
* Gold
* Platinum
* Diamond

The report compares average spend across tiers to identify the most valuable customer groups and retention opportunities.

---

## Visuals Included

### KPI Cards

* Products Sold
* Return Rate %
* Average Customer Spend
* Refund Exposure

### Product Analysis

* Product Performance Matrix
* Revenue Rank
* Margin Rank

### Tag Analysis

* Revenue by Product Tag
* Gross Profit by Product Tag

### Segment Analysis

* Revenue by Customer Segment
* Orders by Customer Segment

### Returns Analysis

* Return Rate by Category
* Return Reason Breakdown
* Refund Exposure by Channel

### Customer Value Analysis

* Average Spend by Loyalty Tier
* Average Spend by Region

---

## Key Business Insights

* Identify the most profitable products and categories.
* Measure performance of product tags and customer segments.
* Detect categories with elevated return rates.
* Evaluate customer lifetime value across regions and loyalty tiers.
* Understand refund exposure and operational risks.
* Support product strategy, inventory planning, and retention initiatives.
