# Salesforce Authentication

This action allows you to Authenticate Salesforce using credential and passing response by performaing `services/oauth2/token`

## Inputs

| Inputs              | Required  | Description |
| :---                | :---:     | :---        |
| sfdc_client_id      | ✅ | The consumer key of the connected app. To access the consumer key, from the App Manager, find the connected app and select View from the dropdown. Then click Manage Consumer Details. You're sometimes prompted to verify your identity before you can view the consumer key. |
| sfdc_client_secret  | ✅ | The consumer secret of the connected app. To access the consumer secret, from the App Manager, find the connected app and select View from the dropdown. Then click Manage Consumer Details. You're sometimes prompted to verify your identity before you can view the consumer secret. |
| username            | ✅ | The username of the user that the connected app is imitating. |
| password            | ✅ | The password of the user that the connected app is imitating. |
| login_url           | ✖️ | **Production** \| **Developer** \| **Devhub** -- `https://login.salesforce.com` `🟢 default` <br> **Sandbox** \| **Scratch** \| -- `https://test.salesforce.com` <br> **MyDomainName** `https://MyDomainName.my.salesforce.com` |

## Inputs suggestions
Use following inputs with **secrets**, [Creating encrypted secrets for a repository](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository) | [Using encrypted secrets in a workflow](https://docs.github.com/en/actions/security-guides/encrypted-secrets#using-encrypted-secrets-in-a-workflow).
- sfdc_client_id
- sfdc_client_secret
- password

## Example usage

```yaml
# SFDC Authentication + using outputs within next step of the same job + error handling
name: SFDC Authentication using details
on: [push]
jobs:
  test:
  
    runs-on: ubuntu-latest
    
    steps:
      - uses: karamchandanid/sfdc-credentials-auth@v19
        id: sfdc_auth
        with:
          sfdc_client_id: ${{ secrets.SFDC_CLIENT_ID }}
          sfdc_client_secret: ${{ secrets.SFDC_CLIENT_SECRET }}
          user_name: ${{ secrets.SFDC_DEV_USERNAME }}
          password: ${{ secrets.SFDC_DEV_PASSWORD }}
          login_url: https://login.salesforce.com
      
      - name: sfdc_auth_details
        run: |
          # Validating access_token if null then terminate the process
          if [[ -z ${{ steps.sfdc_auth.outputs.access_token }} ]] || [[ ${{ steps.sfdc_auth.outputs.access_token }} == "null" ]];
          then 
            echo "----------------------------------"
            echo "Error in salesforce authentication"
            echo "----------------------------------"
            echo "$error ${{ steps.sfdc_auth.outputs.error }} : ${{ steps.sfdc_auth.outputs.error_description }}"
            echo ""
            exit 1;
          fi
          # on success proceed following
          echo ${{ steps.sfdc_auth.outputs.instance_url }}
          echo ${{ steps.sfdc_auth.outputs.id }}
          echo ${{ steps.sfdc_auth.outputs.access_token }}
