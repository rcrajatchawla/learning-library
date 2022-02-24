# Create the Order Page

## Introduction

In this lab, you will create a new page that will allow customers to view the details of their recent order.
Customers will find the details of the order:
- Order number
- Order date
- Status
- Total price
- Quantity and price of the items.

Once you have finished all the steps described in this lab, your page will look like the following image:
![](images/orders-page.png " ")

Estimated Lab Time: 15 minutes

### Objectives
In this lab, you will:
- Create a page to review the items that customer just bought.

## Task 1: Creating a Normal Page - Order Information
Create a Normal Page to review the Order that customer has made.

1. In the **App Builder**, click **Create Page**.
    ![](images/create-page.png " ")
2. Select **Blank Page** and click **Next**.
    ![](images/blank-page.png " ")
3. Enter the following and click **Next**.
    - Page Number - enter **16**
    - Name - enter **Order Information**
    - Page Mode - select **Normal**
    - Breadcrumb - select **- don't use breadcrumbs on page -** 
4. For Navigation Preference, select **Do not associate this page with a navigation menu entry** and click **Next**.
5. Click **Finish**.

## Task 2: Adding a Region 
Add a region to the page to display order details.

1. In the new page created, navigate to the **Gallery Menu**.
2. Drag a **Static Content** region and drop it to the Content Body section.
    ![](images/create-static.png " ")
3. In the Property Editor, enter the following:
    - For Title - enter **Thank you for your order!**
    - For Template - select **Content Block**
    - For Template Options - check **Show Region Icon** and click **Ok**
        ![](images/template-options.png " ")
    - For Icon, enter **fa-heart**

## Task 3: Adding Items to the Page
Add an item to save the order ID.

1. In the Rendering tree (left pane), navigate to the **Thank you for your order!** region.
2. Right click on the region and click on **Create Page Item**.
    ![](images/create-item.png " ")
3. Create one hidden item as follows:

    | Name |  Type  | 
    | --- |  --- | 
    | P16_ORDER | Hidden |

    ![](images/order-item.png " ")    
    
## Task 4: Adding Static Content Region
Add a region to contain Order details and items.

1. In the Rendering tree (left pane), navigate to the **Thank you for your order!** region.
2. Right click on the region and click on **Create Sub Region**.
    ![](images/create-sub-region.png " ")
3. In the Property Editor, enter the following:
    - For Title - enter **Order: &P16_ORDER.**
    - For Type - select **Static Content**

## Task 5: Adding Order Details Region
Add a region to display Order details.

1. In the Rendering tree (left pane), navigate to the **Order: &P16_ORDER.** region.
2. Right click on the region and click on **Create Sub Region**.
    ![](images/create-sub-region2.png " ")
3. In the Property Editor, enter the following:
    - For Title - enter **Order Details**
    - For Type - select **Cards**
    - Under Source section:
        - For Type - select **SQL Query**
        - For SQL Query - enter the following SQL Query:

            ``` 
            <copy>
            SELECT o.order_id,
                o.order_datetime,
                o.customer_id,
                o.order_status,
                o.store_id,
                (SELECT Sum(unit_price * quantity)
                    FROM   order_items i
                    WHERE  i.order_id = o.order_id) total
            FROM   orders o
            WHERE  order_id = :P16_ORDER 
            </copy>
            ```
4. Click on Attributes.
    ![](images/attributes.png " ")
    -  Search for Secondary Body in the filter and do the following:
        - Set Advanced Formatting to **On**
        - For HTML Expression - enter:

            ``` 
            <copy>
            <b> Order Placed:</b> &ORDER_DATETIME. <br> 
            <b> Status: </b>&ORDER_STATUS. <br> 
            <b> Total: </b>&TOTAL.    
            </copy>
            ```

## Task 6: Adding Items Region
Add a region to display items in the order.

1. In the Rendering tree (left pane), navigate to the **Order: &P16_ORDER.** region.
2. Right click on the region and click on **Create Sub Region**.
    ![](images/create-sub-region3.png " ")
3. In the Property Editor, enter the following:
    - For Title - enter **Items**
    - For Type - select **Cards**
    - Under Source section:
        - For Type - select **SQL Query**
        - For SQL Query - enter the following SQL Query: 

            ``` 
            <copy>
            SELECT  o.line_item_id                Item,
                    p.product_name                Product,
                    o.unit_price,
                    o.quantity,
                    ( o.unit_price * o.quantity ) Subtotal,
                    p.product_image
            FROM   order_items o,
                products p
            WHERE  p.product_id = o.product_id
            AND  order_id = :P16_ORDER 
            </copy>
            ```
4. Click on Attributes and do the following:
    ![](images/attributes2.png " ")

    - Under Title section:
        - For Column - select **PRODUCT**    

    - Under Secondary Body:
        - Set Advanced Formatting to **On**
        - For HTML Expression - enter: 
    
            ``` 
            <copy>
            <b>Quantity: </b> &QUANTITY. <br> 
            <b>Unit Price: </b>&UNIT_PRICE.    
            </copy>
            ```

    - Under Media section:
        - For Source - select **BLOB Column**   
        - For BLOB Column - select **PRODUCT_IMAGE**  
        - For Position - select **Body**  
        - For Appearance - select **Auto**  
        - For Sizing - select **Fit**   

    - Under Card:
        - For Primary Key Column 1 - select **ITEM**    

5. Click **Save**.



You now know how to add more pages to your existing APEX Application.

## **Acknowledgments**

- **Author** - Mónica Godoy, Principal Product Manager
- **Last Updated By/Date** - Mónica Godoy, Principal Product Manager, September 2021