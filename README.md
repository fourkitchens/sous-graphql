# Sous GraphQL Recipe

A recipe that contains modules and configuration for Sous default GraphQL configuration.

## Configuring Drupal for Recipes

See <https://www.drupal.org/files/issues/2023-10-01/Configuring%20Drupal%20to%20Apply%20Recipes.md>

## Installing this Recipe

`composer require fourkitchens/sous-graphql`

## Applying this Recipe

If you used the Sous Project as your starterkit:

- `lando install-recipe fourkitchens/sous-graphql`

Manually applying the recipe to your own project: From your webroot run:

- `php core/scripts/drupal recipe recipes/sous-graphql`
- `drush cr`
- `composer unpack fourkitchens/sous-graphql`

## Drupal configuration

### Setting Up OAuth and Configuration
- Create an oauth folder in the private files directory: `web/sites/default/files/private/oauth`
- Go to `/admin/config/people/simple_oauth` click Generate keys, set the Directory for the keys to `/app/web/sites/default/files/private/oauth`, generate, and save the configuration.

## Setting Up hash_salt
- Run lando `lando drush php-eval 'echo \Drupal\Component\Utility\Crypt::randomBytesBase64(55) . "\n";' | pbcopy`. [See more](https://blokspeed.net/2018/quick-tip-generating-hash-salt-drupal-8)
- Go to `web/sites/default/settings.php` and paste the output from the previous command into the hash_salt setting.
```
  $settings['hash_salt'] = 'OUTPUT';
```

### Create Role for GraphQL API
- Create a role with these permissions (e.g., GraphQL API):
  - GraphQL Compose - Server: Execute arbitrary requests
  - View media
  - View own unpublished media
  - View user email addresses
  - View user information

### Create User
- Create a user with the new role (e.g., username: `nextjs` and email `nextjs@test.com`).

### Create Consumer
- Go to `/admin/config/services/consumer`
- Create or modify a consumer:
  - Generate a Client ID and save it for later use in the Front-End config. (just by clicking `Generate random client ID` in the form)
  - Assign the user you created.
  - Set a New Secret (save for Front-End config).
  - Uncheck `Is this consumer 3rd party`.
  - Select the role created in Scopes field.
  - Save.

### Configuring GraphQL Compose
- Pretending you have a content type called `page`
- Create a page node for testing.
- Go to `/admin/config/graphql_compose` and enable:
  - GraphQL Loading by route
  - Fields for content/media types as needed.

### Test GraphQL Queries
- Go to `/admin/config/graphql/servers/manage/graphql_compose_server/explorer`.
- Use the explorer helper (new folder icon in the left sidebar) to build queries, e.g.:
``` graphql
  query MyQuery {
    route(path: "/node/1") {
      ... on RouteInternal {
        __typename
        entity {
          ... on NodePage {
            id
            title
            text {
              value
            }
          }
        }
      }
    }
  }
```
- Click the execute button and verify results.
- Congratulations! You can now run GraphQL queries.
