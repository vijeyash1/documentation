**

## How to install ory cli?

[https://www.ory.sh/docs/guides/cli/installation](https://www.ory.sh/docs/guides/cli/installation)



## List the available projects using cli ?

    ory list projects

## How to get scema Id?

 `ory get project <projectid> --format json | jq | grep default_schema_id`


## How to create a oauth client using cli?

    ory create oauth2-client \  
    --project e682d62c-b226-0371bae9 \  
    --name nodeapp \  
    --grant-type authorization_code,refresh_token \  
    --response-type code,id_token \  
    --scope openid,offline \  
    --redirect-uri http://127.0.0.1:5555/callback

