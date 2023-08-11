# Calculations

##### Order Date
Gathering of separate order year, month, and day fields into a single date.

    MAKEDATE([Order Year], [Order Month], [Order Day])

##### Shipment Date
Gathering of separate shipment year, month, and day fields into a single date.

    MAKEDATE([Shipment Year], [Shipment Month], [Shipment Day])

##### Shipment Days – Actual
The actual number of days that occured between the date the order was placed and the date the shipment was made.

    DATEDIFF('day', [Order Date], [Shipment Date])

##### Shipment Delay (days)
The average difference (positive or negative) between the estimated number of shipment days and the actual number of shipment days.
 
    AVG([Shipment Days - Actual] - [Shipment Days - Scheduled])

##### Shipment Delay
Categories based on the non-aggregated difference in shipping delay.

    IF [Shipment Days - Actual] - [Shipment Days - Scheduled] < 0 THEN 'Before schedule'
    ELSEIF [Shipment Days - Actual] - [Shipment Days - Scheduled] = 0 THEN 'On schedule'
    ELSEIF [Shipment Days - Actual] - [Shipment Days - Scheduled] <= 5 THEN 'Delay up to 5 days'
    ELSEIF [Shipment Days - Actual] - [Shipment Days - Scheduled] > 5 THEN 'Delay over 5 days'
    END

##### Delayed Orders
Distinct count of orders where a shipment occured behind schedule.

    COUNTD(IF[Shipment Delay] = 'Delay up to 5 days'
    OR [Shipment Delay] = 'Delay over 5 days'
    THEN [Order ID] END)

##### % of Delayed Orders
Percentage ratio of orders that occured behind schedule relative to all orders.

    [Delayed Orders]/COUNTD([Order ID])

##### Inventory to Sales Delta
The change in monthly warehouse inventory after accounting for the sales of all orders.

    SUM([Warehouse Inventory]) - SUM([Order Quantity])

##### Overstock or Understock
Classification of whether inventory was overstocked or understocked.

    IF [Inventory to Sales Delta] > 0 THEN 'Overstock'
    ELSE 'Understock' END

##### Monthly Over or Understock
Monthly LOD expression of the amount of overstock (+) or understock (-) in inventory.

    { INCLUDE MONTH([Order Date]) : [Inventory to Sales Delta] }


##### Monthly Over or Understock Cost
Holding/opportunity costs of Monthly Over or Understock.

    AVG([Monthly Over or Understock]) * AVG([Inventory Cost Per Unit])
