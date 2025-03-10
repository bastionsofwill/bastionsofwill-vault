You're close!

boto3에서는  `boto3.client()` or `boto3.resource()` 함수를 호출하여 사용자가 client or resource 객체를 직접 생성할 수 있다. 
이 때, AWS credential 또한 사용할 수 있다.

```python
import boto3

# Create a client with custom credentials
bedrock_runtime = boto3.client(
    service_name="bedrock-runtime",
    region_name="us-west-2",
    aws_access_key_id='YOUR_ACCESS_KEY',
    aws_secret_access_key='YOUR_SECRET_KEY'
)
```
 
또는, Session 객체를 먼저 생성하고 이를 사용하여 client를 생성하는 것도 가능하다.

```python
import boto3

# Create a session with custom credentials
session = boto3.Session(
    aws_access_key_id='YOUR_ACCESS_KEY',
    aws_secret_access_key='YOUR_SECRET_KEY',
    region_name='us-west-2'
)

# Use the session to create the Bedrock Runtime client
bedrock_runtime = session.client('bedrock-runtime')
```

Both approaches work, but creating a Session object can be more flexible and convenient if you need to reuse the same set of credentials across multiple clients or resources.

Note that it's not recommended to hard-code your AWS access key in code. Consider using environment variables, IAM roles, or other secure methods to manage your AWS credentials.