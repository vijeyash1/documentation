**a simple go program to create a oauth client**

   

     package main
    
      
    
    import (
    
    "bytes"
    
    "encoding/json"
    
    "fmt"
    
    "net/http"
    
    )
    
      
    
    func  main() {
    
    client := &http.Client{}
    
      
    
    // Create the request body
    
    body := map[string]interface{}{
    
    "client_name": "check-client",
    
    "client_secret": "my-secret",
    
    "grant_types": []string{"client_credentials", "refresh_token","authorization_code"},
    
    "response_types": []string{"code", "token", "id_token"},
    
    "scope": "offline openid email",
    
    "token_endpoint_auth_method": "client_secret_basic",
    
    // client_secret_basic, client_secret_post, none
    
    "redirect_uris": []string{"https://www.ory.sh/"},
    
    }
    
    jsonBody, _ := json.Marshal(body)
    
    token := "ory_pat_ACe9AwgTyr9cmmmmmmmmm"
    
    // Create the request
    
    req, _ := http.NewRequest("POST", "https://put yours-2yiv.projects.oryapis.com/admin/clients", bytes.NewBuffer(jsonBody))
    
    req.Header.Add("Content-Type", "application/json")
    
    req.Header.Add("Authorization", "Bearer "+token)
    
      
    
    // Make the request
    
    resp, _ := client.Do(req)
    
      
    
    // Parse the response
    
    var  clientResp  map[string]interface{}
    
    json.NewDecoder(resp.Body).Decode(&clientResp)
    
      
    
    fmt.Println("Client ID:", clientResp["client_id"])
    
    fmt.Println("Client Secret:", clientResp["client_secret"])
    
    }
