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

