# Shopify API

**Currently a Work in Progress**

This package allows Elixir developers to easily access the admin Shopify API. 

## Installation

The package can be installed by adding `shopify` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [{:shopify, "~> 0.1.1"}]
end
```

## Getting Started

The Shopify API can be accessed in two ways - either with private apps via basic auth, or with oauth.

### Private Apps

Once you have a valid API key and password, setup your `config/confix.exs`.

```elixir
config :shopify, [
  shop_name: "my-shop",
  api_key: System.get_env("SHOPIFY_API_KEY"),
  password: System.get_env("SHOPIFY_API_PASSWORD")
]
```

We can now easily create a new API session.

```elixir
Shopify.session
```

Alternatively, we can create a one-off session.

```elixir
Shopify.session(:basic, {"my-shop-name", "my-api-key", "my-password"})
```

### OAuth Apps

**Support for access toke creation coming soon**

We can also access the API with valid OAuth credentials.

```elixir
Shopify.session(:oauth, {"shop-name", "access-token"})
```

## Making Requests

All API requests require a session struct to begin.

```elixir
Shopify.session(:oauth, {"shop-name", "access-token"}) |> Shopify.Product.find(1)

# OR

session = Shopify.session(:oauth, {"shop-name", "access-token"})
Shopify.Product.find(session, 1)
```

Here are some examples of the various types of requests that can be made.

```elixir
# Create a session struct
session = Shopify.session(:oauth, {"shop-name", "access-token"})

# Find a resource by ID
{:ok, resource} = session |> Shopify.Product.find(id)

# Find a resource and select fields
{:ok, resource} = session |> Shopify.Product.find(id, %{fields: "id,images,title"})

# All resources
{:ok, resources} = session |> Shopify.Product.all

# All resources with query params
{:ok, resources} = session |> Shopify.Product.all(%{page: 1, limit: 5})

# Find a resource and update it
{:ok, resource} = session |> Shopify.Product.find(id)
updated_resource = %{resource | title: "New Title"}
{:ok, resource} = session |> Shopify.Product.update(updated_resource.id, updated_resource)

# Create a resource
new_product = %Shopify.Product{
	title: "Fancy Shirt",
    body_html: "<strong>Good shirt!<\/strong>",
    vendor: "Fancy Vendor",
    product_type: "shirt",
    variants: [
    	%{
   			price: "10.00",
    		sku: 123
   		}]
    }
{:ok, resource} = session |> Shopify.Product.create(new_product)

# Count resources
{:ok, count} = session |> Shopify.Product.count

# Count resources with query params
{:ok, count} = session |> Shopify.Product.count(%{vendor: "Fancy Vendor"})


# Search for resources
{:ok, resource} = session |> Shopify.Customer.search(%{query: "country:United States"})

# Delete a resource
{:ok, _} = session |> Shopify.Product.delete(id)
```

Results are all returned in the form of a two-item tuple.

```elixir
# Create a session struct
session = Shopify.session(:oauth, {"shop-name", "access-token"})

# 'resource' is returned as a %Shopify.Product struct
{:ok, resource} = session |> Shopify.Product.find(id)

# 'resources' is returned as a list of %Shopify.Product structs
{:ok, resources} = session |> Shopify.Product.all

# 'error' is returned as a %Shopify.Error struct
{:error, error} = session |> Shopify.Product.find(1)
```

## Current Resources

- Address
- BillingAddress
- Checkout (all, count)
- ClientDetails
- Customer (find, all, create, update, delete, count, search)
- DiscountCode
- Fullfillment
- Image
- LineItem
- Option
- Order (find, all, create, update, delete, count)
- PaymentDetails
- Product (find, all, create, update, delete, count)
- ShippingAddress
- ShippingLine
- TaxLine
- Variant


Documentation can be generated with [ExDoc](https://github.com/elixir-lang/ex_doc)
and published on [HexDocs](https://hexdocs.pm). Once published, the docs can
be found at [https://hexdocs.pm/shopify](https://hexdocs.pm/shopify).