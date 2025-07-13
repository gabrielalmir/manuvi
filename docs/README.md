# Manuvi Documentation

Welcome to the official documentation for **Manuvi** – an intelligent production-planning platform.

## :card_index_dividers: Contents

- **[Project Readme](../README.md)** – high-level overview
- **[Architecture](./ARCHITECTURE.md)** – technical vision and structure
- **[Development Guide](./DEVELOPMENT.md)** – local setup and contribution workflow
- **[API](./API.md)** – REST interface (draft)
- **[Database](./DATABASE.md)** – data model (draft)
- **[Authentication](./AUTH.md)** – UI flows and planned backend endpoints

---

## :information_source: Current Status

### Front-end
- Next.js 14 (App Router) with TypeScript, Tailwind CSS and Zustand state management ([ARCHITECTURE.md](./ARCHITECTURE.md)).
- Implemented pages: authentication (mock), dashboard, products, machines, orders, planning, reports and team management.
- User authentication is **mock-only** – there is no backend integration yet ([AUTH.md](./AUTH.md)).

### Back-end
- Python 3.11 project with FastAPI entry-point stub and typed domain entities ([ARCHITECTURE.md](./ARCHITECTURE.md)).
- Database, JWT authentication and business services are **planned** but not implemented ([API.md](./API.md), [DATABASE.md](./DATABASE.md)).

> :warning: Manuvi is currently an open-source proof of concept used as a portfolio project. Future releases may be published under a different (private or commercial) licence. See the root [README](../README.md) for details.

## :busts_in_silhouette: Intended Audience

- **Developers** – React, Next.js, TypeScript, Python / FastAPI
- **Product managers** – high-level architecture and roadmap
- **System administrators** – local installation guidelines

## :memo: Contribution

Pull requests are welcome. Please follow the conventions described in the [Development Guide](./DEVELOPMENT.md).

## :link: Support & Licensing

For commercial licences or other questions, contact me on [LinkedIn](https://linkedin.com/in/gabrielalmir).

```
