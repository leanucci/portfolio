# Projects

The projects in my portfolio.

| Field | Meaning |
|---|---|
| Type | `library` or `web app` |
| Cycle | `agent`: the project uses the agent cycle in [APPROACH.md](APPROACH.md). `manual`: it does not use the cycle yet. |

## bravo

- **Type:** library (Ruby gem)
- **Summary:** Ruby gem for Argentine electronic invoicing. It gets the CAE (electronic authorization code) from the AFIP WSFE web service.
- **Repo:** https://github.com/leanucci/bravo
- **Package:** https://rubygems.org/gems/bravo
- **Local copy:** `/Users/lean/work/afip/ruby/bravo`
- **Cycle:** manual

## wsaa-ruby

- **Type:** library (Ruby gem)
- **Summary:** Ruby client for the AFIP WSAA authentication service. It gets the TOKEN and SIGN credentials that other AFIP web services need.
- **Repo:** https://github.com/leanucci/wsaa-ruby
- **Package:** https://rubygems.org/gems/wsaa-ruby
- **Local copy:** `/Users/lean/work/afip/ruby/wsaa-ruby`
- **Cycle:** manual

## pomodoro

- **Type:** web app
- **Summary:** A Pomodoro timer web app. Users run focus sessions and breaks and see their history. Google sign-in and a database come later.
- **Stack:** Next.js, TypeScript, Tailwind CSS. Later: Auth.js and Postgres on Neon.
- **Repo:** https://github.com/leanucci/pomodoro
- **Deploy:** Vercel
- **Local copy:** `/Users/lean/work/pomodoro`
- **Cycle:** agent
