# historicalsales

Requirements:
1. Create Business Rule "customer changed" in LSX and connect to pipedream trigger module
2. Create Custom Field in LSX: entity "sale", type "string", title/api-name "loyalty-value-options"
3. Create a Google sheet with sales data
4. Add Get_values_in_range step after the trigger and then select your sheet in there as in the screenshot
![Screenshot 2024-10-25 at 9 32 12 AM](https://github.com/user-attachments/assets/6de61db3-bad4-44c0-abc0-38b3371500d6)



Use:
1. Search for all historical sales for a customer
2. Look up sale by Order ID
3. Look up customer balance for account payments
Notes:
Currently we are just pulling the records based on hardcoded customer from the Google sheet

Then the Order ID is being displayed on the Pop UP

The problem I am facing is:
1. When we click continue it just loops back and displays the pop-up again. Even though the custom field on the sale is set
2. The workflow times out the first time it is run and the second time it works


export default defineComponent({
  async run({ steps, $ }) {
    const values = steps.get_values_in_range.$return_value;
    const targetValue = "JOAN RYLAH";

    let foundRow = null;

    for (const row of values) {
      if (row.includes(targetValue)) {
        foundRow = row;
        break; // Exit the loop once the target value is found
      }
      else
        foundRow = row;
    }

    if (foundRow) {
      const action = {
        "actions": [
          {
            "type": "require_custom_fields",
            "title": "Historical Sales",
            "message": "The sale id is " + foundRow[0],
            "entity": "sale",
            "required_custom_fields": [
              {
                "name": "Order-ID"
              }
            ]
          }
        ]
      };

      await $.respond({
        status: 200,
        headers: { accept: 'application/json', 'content-type': 'application/json' },
        body: action
      });
    } else {
      await $.respond({
        status: 404,
        headers: { accept: 'application/json', 'content-type': 'application/json' },
        body: { message: "Target value not found" }
      });
    }
  }
});
