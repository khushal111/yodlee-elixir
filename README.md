# Yodlee

Yodlee Aggregation API for Elixir

## Use

1. Get cobrand session token

```ex
%{"cobrandConversationCredentials" =>  %{"sessionToken" => session_token} } = Yodlee.Cobrand.login cobrand_username, cobrand_password
```

## Installation

If [available in Hex](https://hex.pm/docs/publish), the package can be installed as:

  1. Add yodlee to your list of dependencies in `mix.exs`:

        def deps do
          [{:yodlee, "~> 0.0.1"}]
        end

  2. Ensure yodlee is started before your application:

        def application do
          [applications: [:yodlee]]
        end
