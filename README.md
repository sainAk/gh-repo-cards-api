## GitHub repo info api

This is a simple cloudflare worker script that will fetch the github repo metadata for given repos from the github graphql api.

### Usage

To run the script:

obtain a github access token from https://github.com/settings/tokens/new?description=public_read_api

⚠️ make sure the scope of the token is limited to read only

add the token to wrangler.toml and run 

```shell
npx wrangler@beta dev index.js -l
```

Example request

```shell
curl --request POST \
  --url http://localhost:8787/ \
  --header 'Content-Type: application/json' \
  --data '{
        "repos": [
            "sainak/expensinator",
            "torvalds/linux"
        ]
    }'
```

GraphQL query 

```graphql
query {
    fragment repoProperties on Repository {
        nameWithOwner
        description
        openGraphImageUrl
        homepageUrl
        forkCount
        stargazerCount
        updatedAt
        licenseInfo {
            spdxId
        }
        issues(states:OPEN) {
            totalCount
        }
        languages(first: 100) {
            totalSize
            edges {
                size
                node {
                    name
                    color
                }
            }
        }
        repositoryTopics(first: 10) {
            nodes {
                topic {
                    name
                }
            }
        }
    }
    {
        repo1:repository(owner: "sainak", name: "expensinator") { ...repoProperties }
        repo2:repository(owner: "torvalds", name: "linux") { ...repoProperties }
        rateLimit {
            limit
            cost
            remaining
            resetAt
        }
    }
}
```

### GitHub Api

https://docs.github.com/en/graphql