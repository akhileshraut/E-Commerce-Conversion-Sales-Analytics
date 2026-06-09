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

