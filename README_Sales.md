# Sales & Orders Analysis

تحليل بيانات أوردرات ومبيعات باستخدام **Power BI**، بيهدف لتحويل بيانات خام (CSV/Excel) لموديل بيانات منظم (Star Schema) يسهل عمل تقارير وتحليلات على أداء المبيعات.

---

## 📊 نظرة عامة

المشروع بيحلل بيانات أوردرات على مستوى **تفاصيل كل سطر (Order Line Level)**، وبيغطي معلومات زي الكمية المباعة، السعر، الضريبة، الشحن، والإجمالي المستحق، بالإضافة لبيانات العميل، مندوب المبيعات، والمنطقة الجغرافية.

---

## 🗂️ Data Model (Star Schema)

**Fact Table — Sales:**
| العمود | النوع | الوصف |
|---|---|---|
| OrderQty | Measure | الكمية المباعة |
| UnitPrice | Measure | سعر الوحدة |
| LineTotal | Measure | صافي قيمة السطر (Qty × UnitPrice) |
| TaxAmt | Measure | قيمة الضريبة |
| Freight | Measure | تكلفة الشحن |
| TotalDue | Measure | الإجمالي المستحق (LineTotal + TaxAmt + Freight) |
| OrderDetailID, OrderID | Degenerate Dimension | مفاتيح مرجعية للأوردر تعيش جوه الـ Fact table |

**Dimension Tables:**
| الجدول | الأعمدة |
|---|---|
| Dim_Date | OrderDate |
| Dim_Status | StatusID, Status |
| Dim_Customer | CustomerID |
| Dim_Product | ProductID |
| Dim_SalesPerson | SalesPersonID |
| Dim_Territory | TerritoryID, Territory, TerritoryGroup |

---

## 🧮 أهم DAX Measures

```dax
-- المبيعات الصافية (قبل الضريبة والشحن)
Total Sales = SUM(Sales[LineTotal])

-- الإجمالي المستحق (شامل كل شيء)
Total Due = SUM(Sales[TotalDue])

-- الكمية والضريبة والشحن
Total Quantity = SUM(Sales[OrderQty])
Total Tax = SUM(Sales[TaxAmt])
Total Freight = SUM(Sales[Freight])

-- متوسطات
Average Unit Price = AVERAGE(Sales[UnitPrice])
Average Order Value = DIVIDE([Total Sales], DISTINCTCOUNT(Sales[OrderID]))

-- عدد الأوردرات والعملاء
Total Orders = DISTINCTCOUNT(Sales[OrderID])
Total Customers = DISTINCTCOUNT(Sales[CustomerID])

-- نسب
Tax % of Sales = DIVIDE([Total Tax], [Total Sales])
Freight % of Sales = DIVIDE([Total Freight], [Total Sales])

-- تحليل حسب الحالة
Shipped Orders = CALCULATE([Total Orders], Sales[Status] = "Shipped")
Backordered Orders = CALCULATE([Total Orders], Sales[Status] = "Backordered")
```

---

## 📈 صفحات الداشبورد

**1) Home (Sales Analysis)**
- KPI Cards: Total Sales & LY (مع Goal), Total Due, YoY%, Total Tax, Total Sales, Total Orders, Total Freight, Total Quantity, Tax % of Sales
- أزرار تنقل (Navigation Buttons): Product, Sales

**2) Product Analysis**
- KPI Cards: Total Sales, Total Order, Total Quantity
- Line Chart: Total Sales by Month
- Pie Chart: Total Order by ProductCategory
- Table: ProductCategory, Total Order, Total Sales
- Bar Chart: Top 5 by Product
- Bar Chart: Bottom 5 by Product
- Slicers: Year, Category, Territory

**3) Sales Analysis (by Territory)**
- KPI Cards: Total Tax, Total Due, Total Sales
- Bar Chart: Top 5 by Territory
- Bar Chart: Total Sales by SubCategory
- Treemap: Total Sales by ProductSubCategory
- Decomposition Tree: Total Sales حسب ProductCategory → ProductSubCategory
- Slicers: Year, Category, Territory

---

## 🛠️ الأدوات المستخدمة

- Power BI Desktop (Data Modeling, DAX)
- Power Query (Data Cleaning & Transformation)

---

## 📁 محتويات الريبو

```
├── data/                  # ملف الأوردرات الخام
├── PowerBI/               # ملف .pbix
└── README.md
```

---

## 👤 إعداد

تم بناء وتحليل الموديل باستخدام Power BI (Data Modeling + DAX).
