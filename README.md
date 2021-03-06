# Yodlee-Elixir [![Build Status](https://travis-ci.org/levanto-financial/yodlee-elixir.svg?branch=master)](https://travis-ci.org/levanto-financial/yodlee-elixir)


Yodlee YSL API for Elixir 

## Example Use

**1)** Setup your cobrand credentials, and run the main script

```sh
COBRAND_USERNAME=sbCobREPLACE_WITH_COBRAND_USERNAME \
COBRAND_PASSWORD=REPLACE_WITH_COBRAND_PASSWORD \
EXAMPLE_USER_LOGIN=A_TEST_USER_LOGIN \
EXAMPLE_USER_PASSWORD=A_TEST_USER_PASSWORD \
iex -S mix run lib/yodlee.ex 

```

**2)** Configure the plugin for your app (optional)
```ex
config :yodlee,
  # request_handler: Yodlee.Handler, # Good for testing w/o hitting the real API
  api_base_url: "https://developer.api.yodlee.com/ysl/"
```

`api_base_url` defaults to `https://developer.api.yodlee.com/ysl/`

**3)** Play!

You can login as your registered cobrand, and then make other API calls that depend on that token
```ex

case Yodlee.as_cob(
      System.get_env("COBRAND_USERNAME"),
      System.get_env("COBRAND_PASSWORD"),
      fn session_token -> 
        "Your session token is #{session_token}"
      end) do
  {:ok, result} -> IO.puts "Result: #{result}"
  {:error, result} -> IO.inspect result
end

# Result: Your session token is 08qd91........dqwdd

```

You may also login as a user associated with your cobrand, to get the user session token

```ex
r = case Yodlee.as_cob_and_user(
  System.get_env("COBRAND_USERNAME"),
  System.get_env("COBRAND_PASSWORD"),
  System.get_env("EXAMPLE_USER_LOGIN"),
  System.get_env("EXAMPLE_USER_PASSWORD"), fn {cob_tok, user_tok} ->
    "User Token: #{user_tok}"
  end) do
  {:ok, result} -> result
  {:error, err} -> raise err
end

IO.inspect r

# User Token: 080620198a32...12e09

```

And then user that user session token to do something like search for banking institutions

```ex

case Yodlee.as_cob(
  System.get_env("COBRAND_USERNAME"),
  System.get_env("COBRAND_PASSWORD"), fn cob_tok ->
    Yodlee.Provider.search(cob_tok, %{"name" => "Star One"})
  end) do
  {:ok, result} ->
    Enum.each result["provider"], fn p -> 
      IO.puts p["name"]
    end
  {:error, err} -> raise err
end

# Star One FCU
# STAR Financial Bank
# Five Star Bank (NY)
# CENTRAL STAR CU
# Lone Star CU
# Star Harbor FCU - Investments
# Lone Star National Bank
# STAR CU - Investments
# Five Star Bank (CA)
# .......

```

Or you could create and then immediately delete a user

```ex

case Yodlee.as_cob(
  System.get_env("COBRAND_USERNAME"),
  System.get_env("COBRAND_PASSWORD"), fn cob_tok ->

    IO.puts "======== CREATE A USER ========"

    {:ok, new_user} = Yodlee.User.create cob_tok, %{
      "user" => %{
        "loginName" => "mike-logina",
        "password" => "aho12eoed12d$$Aa",
        "email" => "test-1241nd12a@example.com"}}
      


    IO.puts "======== AND DELETE IT ========"

    user_tok = new_user["user"]["session"]["userSession"]
    Yodlee.User.delete cob_tok, user_tok

    new_user
  end) do
  {:ok, result} ->
    IO.inspect result
  {:error, err} -> raise err
end

```

## API Coverage

- [x] **get** - /{cobrandName}/v1/cobrand/publicKey
- [x] **post** - /{cobrandName}/v1/cobrand/logout
- [x] **post** - /{cobrandName}/v1/cobrand/login
- [x] **post** - /{cobrandName}/v1/user/register
- [ ] **get** - /{cobrandName}/v1/user
- [ ] **get** - /{cobrandName}/v1/user/credentials/token
- [ ] **post** - /{cobrandName}/v1/user/credentials
- [x] **delete** - /{cobrandName}/v1/user/unregister
- [ ] **post** - /{cobrandName}/v1/user/samlRegister
- [ ] **post** - /{cobrandName}/v1/user/samlLogin
- [x] **post** - /{cobrandName}/v1/user/logout
- [x] **post** - /{cobrandName}/v1/user/login
- [x] **get** - /{cobrandName}/v1/accounts/{accountId}
- [ ] **post** - /{cobrandName}/v1/accounts/{accountId}
- [x] **delete** - /{cobrandName}/v1/accounts/{accountId}
- [ ] **get** - /{cobrandName}/v1/accounts/investmentPlan/investmentOptions
- [ ] **get** - /{cobrandName}/v1/accounts/historicalBalances
- [x] **get** - /{cobrandName}/v1/accounts
- [ ] **get** - /{cobrandName}/v1/holdings/holdingTypeList
- [ ] **get** - /{cobrandName}/v1/holdings/assetClassificationList
- [ ] **get** - /{cobrandName}/v1/holdings
- [x] **post** - /{cobrandName}/v1/providers/{providerId}
- [ ] **put** - /{cobrandName}/v1/providers/{providerAccountId}
- [ ] **get** - /{cobrandName}/v1/providers/token
- [ ] **get** - /{cobrandName}/v1/providers/{providerId}
- [x] **get** - /{cobrandName}/v1/providers
- [ ] **get** - /{cobrandName}/v1/transactions
- [ ] **get** - /{cobrandName}/v1/transactions/transactionCategoryList
- [ ] **post** - /{cobrandName}/v1/transactions/{transactionId}
- [ ] **get** - /{cobrandName}/v1/transactions/categories/rules
- [ ] **post** - /{cobrandName}/v1/transactions/categories/rules
- [ ] **put** - /{cobrandName}/v1/transactions/categories/rules/{ruleId}
- [ ] **delete** - /{cobrandName}/v1/transactions/categories/rules/{ruleId}
- [ ] **post** - /{cobrandName}/v1/transactions/categories/rules/{ruleId}
- [ ] **post** - /{cobrandName}/v1/transactions/categories/rules
- [ ] **get** - /{cobrandName}/v1/transactions/count
- [ ] **get** - /{cobrandName}/v1/statements
- [ ] **get** - /{cobrandName}/v1/derived/holdingSummary
- [ ] **get** - /{cobrandName}/v1/derived/networth
- [x] **post** - /{cobrandName}/v1/refresh
- [x] **get** - /{cobrandName}/v1/refresh
- [x] **get** - /{cobrandName}/v1/refresh/{providerAccountId}

## Installation

If [available in Hex](https://hex.pm/docs/publish), the package can be installed as:

  1. Add yodlee to your list of dependencies in `mix.exs`:

        def deps do
          [{:yodlee, "~> 0.1.4"}]
        end

  2. Ensure yodlee is started before your application:

        def application do
          [applications: [:yodlee]]
        end
