# Problem Statement 1

According to the problem statement, only front-side validation is working. Back-side validation is not working, but the user is still able to move to the next step easily. The expected result is that both sides of the card should be mandatory.
For that, I found an error in the conditions section and made changes. I ensured that both sides are mandatory.

## Updated Conditions
```json
{
  "conditions": {
    "condition_post_face_match": {
      "rule": "module_face_match_api.action == 'pass'",
      "if_true": "auto_approve",
      "if_false": "auto_decline"
    },
    "condition_post_idcard_validation": {
      "rule": "module_id_card_validation.front_action == 'pass' && module_id_card_validation.back_action == 'pass'",
      "if_true": "module_selfie_validation",
      "if_false": "auto_decline"
    },
    "condition_post_aadhar_api": {
      "rule": "module_aadhar_api.statusCode == 200",
      "if_true": "auto_approve",
      "if_false": "auto_decline"
    }
  }
}
```

# Problem Statement 2

According to the problem statement, API workflow is very unstable and we have to made an another API module called "Check Aadhar Status.

##### Condition: 

```  => If API is unavailable/down redirect the user to ID Card Validation module.```

In this API, I used a GET request to retrieve data from the server, and checking the status whether it is up or down. Here up means working fine and down means server unavailable.

```=> When the server status == "up" then its redirect to module_aadhar_api```

```=> When the Server status == "down" then its redirect to module_id_card_validation.```

```json

  {
    "type": "api",
    "id" : "module_check_aadhar_status",
    "name": "Check Aadhar Status API",
    "nextStep": "condition_aadhar_status_check",
    "properties":{
      "url": "https://dummyapi.hyperverge.co/api/checkAadhaarStatus",
      "apiType": "get"
    },
    "variables": [
      {
        "name": "status",
        "path": "response.status",
        "possible_values": ["up", "down"]
      }
    ]
  }

  {
  "conditions": {
    "condition_aadhar_status_check": {
    "rule": "module_check_aadhar_status.status == 'up'",
    "if_true": "module_aadhar_api",
    "if_false": "module_id_card_validation"  
}
  }
}

```