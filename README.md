# RedbrickAIStacks

This deploys three separate RedbrickAI stacks (or services) with slightly different configurations
    
    Accounts - exposes an unauthenticated Create API
    
    Projects - exposes entities (projects, users, billing) and apis (create, list, get, update, delete) authenticated 
              via a 'Client'-UserPool-projects. 
              All the resources deployed are tagged with the account id making billing possible to the level per account. 
              The user pool is associated with an IdentityPool as well as a client App. 
    
    Tasks - Same as above.
    
Basically, the account stack is always deployed.
Each account registration creates a two stacks per account - a project stack and a task stack each authenticated against a 
        separate user pools. 
Currently a set of lambdas are deployed for every new account. In future, the Lambdas will be predeployed. These yaml will then 
use only the arn of these.
    
To test, sls deploy the 3 ymls. The client name (defaults to a2d) can be overridden from the CLI.

Then introduce users into the pools (correct the client-id and user-pool-id below)

aws cognito-idp sign-up  --region us-east-1 --client-id <70mno34kcd34ip8fgtvvptdpvj> --username admin@example.com --password Passw0rd!

aws cognito-idp admin-confirm-sign-up --region  us-east-1 --user-pool-id <us-east-1_a78tK19m1>  --username admin@example.com

Invoke the api with this user. Again use the correct values for each parameter.

npx aws-api-gateway-cli-test --username='admin@example.com' --password='Passw0rd!' --user-pool-id="us-east-1_R2OmRHnI6" 
    --app-client-id="56ed5mftev8ntqkm6uan7vj6k0" --cognito-region='us-east-1' 
    --identity-pool-id='us-east-1:a52ef563-b9a4-4d69-bc38-60f140987bf2' 
    --invoke-url='https://rsu5dbyb39.execute-api.us-east-1.amazonaws.com/dev' 
    --api-gateway-region='us-east-1' --path-template='/notes'  --method='POST' 
    --body='{"content":"hello world","attachment":"hello.jpg"}'    
    
    
Project level billing and authorization can either be implemented similarly (new set of pools per project instead of just accounts) or by hand. 

This is based on the serverless-stack-demo-api repository.
