package main

import (

"context"

"fmt"

"log"

ory "github.com/ory/client-go"

)

var client = NewSDK()

// Use this context to access Ory APIs which require an Ory API key.

var oryAuthedContext = context.WithValue(context.Background(), ory.ContextAccessToken, "ory_pat_xxxxxxxx")

func NewSDK() \*ory.APIClient {

conf := ory.NewConfiguration()

conf.Servers = ory.ServerConfigurations{{

URL: "https://unruffled-xxxxxxxx.projects.oryapis.com",

}}

return ory.NewAPIClient(conf)

}

func main() {

identity, res, err := client.IdentityApi.CreateIdentity(oryAuthedContext).CreateIdentityBody(ory.CreateIdentityBody{

SchemaId: "preset://email",

Traits: map[string]interface{}{

"email": "vijeyashdeess@gmail.com",

},

}).Execute()

if err != nil {

log.Println(err.Error())

}
fmt.Println(identity.Id, res)
}
