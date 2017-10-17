# Practice Mobilizer

Sikka Practice Mobilizer is an integrated suite of tools offering full control of your practice on your mobile device.For more details : https://practicemobilizer.com/

## **GET** fee_schedule_optimizer

>https://api.sikkasoft.com/v2/fee_schedule_optimizer?request_key=request_key&practice_id={practice_id}&startdate={startdate}&enddate={enddate}

This API to optimize fees for your practice.

Parameter |   Description | Required?
------- | -------- | ------- 
request_key | Valid 32 characters alpha-numeric request key |   Yes
practice_id|  Practice ID|  Yes
startdate  |  format: yyyy-mm-dd, start date |  Yes
enddate | format: yyyy-mm-dd, end date  |   Yes
zipcode|  Zipcode|  No
fee_name  |   Name of fee schedule  |   No