# Tableau Calculated Fields

## Dataset Compatibility

The `dashboard_sales_data.xlsx` dataset contains 4,200 records and includes every source field required for the calculated fields:

- `sales`
- `profit`
- `order_id`
- `customer_id`
- `return_flag`
- `delivery_days`

Tableau may display underscored column names in title case, such as `Order Id`, `Return Flag`, and `Delivery Days`.

## How to Create a Calculated Field

For each calculation:

1. Open a worksheet in Tableau.
2. Select **Analysis > Create Calculated Field**.
3. Enter the calculated-field name.
4. Enter the corresponding formula.
5. Click **OK**.

## 1. Profit Margin

### Formula

```tableau
IF SUM([Sales]) = 0 THEN
    0
ELSE
    SUM([Profit]) / SUM([Sales])
END
```

### Explanation

Profit Margin measures the proportion of sales retained as profit. Using aggregated profit divided by aggregated sales produces the correct weighted margin for any region, category, segment or dashboard selection.

### Format

Set the default number format to **Percentage**, with one decimal place.

## 2. Cost

### Formula

```tableau
[Sales] - [Profit]
```

### Explanation

Cost represents the portion of sales that was not retained as profit. This is a row-level calculation and can be aggregated using `SUM(Cost)` in a visualization.

### Format

Set the number format to **Number (Custom)** with two decimal places.

## 3. Average Order Value

### Formula

```tableau
IF COUNTD([Order Id]) = 0 THEN
    0
ELSE
    SUM([Sales]) / COUNTD([Order Id])
END
```

### Explanation

Average Order Value shows the average recorded sales value generated per unique order. `COUNTD` prevents repeated order rows from overstating the number of orders if the data granularity changes later.

### Format

Set the number format to **Number (Custom)** with two decimal places.

## 4. Return Rate

### Formula

```tableau
IF COUNTD([Order Id]) = 0 THEN
    0
ELSE
    SUM([Return Flag]) / COUNTD([Order Id])
END
```

### Explanation

`Return Flag` equals 1 for a returned order and 0 otherwise. Dividing the total returned flags by the distinct number of orders calculates the share of orders that were returned.

### Format

Set the default number format to **Percentage**, with one decimal place.

## 5. Shipping Delay Bucket

### Formula

```tableau
IF [Delivery Days] < 0 THEN
    "Invalid"
ELSEIF [Delivery Days] <= 2 THEN
    "Fast: 0-2 Days"
ELSEIF [Delivery Days] <= 5 THEN
    "Standard: 3-5 Days"
ELSE
    "Delayed: 6+ Days"
END
```

### Explanation

This calculation converts the numeric delivery duration into operationally useful groups:

- **Fast:** zero to two days
- **Standard:** three to five days
- **Delayed:** six days or more
- **Invalid:** a negative delivery duration

This dimension can be used for colour, labels, filters and comparisons with customer ratings or return rates.

## Optional Additional Calculated Fields

### Loss-Making Order

```tableau
IF [Profit] < 0 THEN
    "Loss"
ELSE
    "Profitable"
END
```

This field separates profitable orders from orders producing a loss.

### Sales per Customer

```tableau
IF COUNTD([Customer Id]) = 0 THEN
    0
ELSE
    SUM([Sales]) / COUNTD([Customer Id])
END
```

This field measures average sales generated per distinct customer.

## Recommended Validation

After creating the fields:

1. Place each numeric calculated field on **Text** in a temporary worksheet.
2. Format Profit Margin and Return Rate as percentages.
3. Place Shipping Delay Bucket on **Rows** and `COUNTD(Order Id)` on **Columns**.
4. Confirm that the bucket counts add up to the total number of distinct orders.
5. Confirm that Cost plus Profit equals Sales at row level.
