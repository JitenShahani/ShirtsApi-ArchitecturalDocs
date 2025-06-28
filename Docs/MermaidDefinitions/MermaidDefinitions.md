# 🧾 Source Code to generate Mermaid Charts.

This document contains the raw Mermaid source used to generate the sequence diagrams for each endpoint in the Shirts API documentation. All charts follow a common flow: JWT authentication, claim enforcement, filter execution, and database interaction.

## 📥 GET /api/shirts


```Mermaid
---
config:
	theme: redux-dark-color
---
sequenceDiagram
	autonumber
	title Shirts API

	participant Client
	participant AuthController
	participant AppRepository
	participant Authenticator
	participant ShirtsController
	participant JWTAuth as 🔐 JWTAuthTokenFilterAttribute
	participant ClaimCheck as 🔒 RequiredClaimAttribute
	participant CreateFilter as 🧪 Shirt_ValidateCreateShirtFilter
	participant UpdateFilter as 🧪 Shirt_ValidateUpdateShirtFilter
	participant IdFilter as 🧪 Shirt_ValidateShirtIdFilter
	participant SizeCheck as 📏 Shirt_EnsureCorrectSizing
	participant Exception as 💥 ExceptionFilter
	participant Db as 🗄️ AppDbContext

	Client->>AuthController: POST /auth (ClientId + Secret)
	AuthController->>AppRepository: GetApplicationByClientId
	AppRepository-->>AuthController: Application + Scopes
	AuthController->>Authenticator: CreateToken(...)
	Authenticator-->>AuthController: JWT with claims
	AuthController-->>Client: 200 OK { Access_Token, Expires_At }

	Client->>Client: Add header:
	Note right of Client: Authorization: Bearer eyJ.hbG.cij...

	Client->>ShirtsController: GET /api/shirts
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("read", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>Db: Get all shirts
	Db-->>ShirtsController: List<Shirt>
	ShirtsController-->>Client: 200 OK

	Note over Client, ShirtsController: 🛂 Every secured request requires sending the Bearer token in the Authorization header
```

## 📥 GET /api/shirts/{id}

```Mermaid
---
config:
	theme: redux-dark-color
---
sequenceDiagram
	autonumber
	title Shirts API

	participant Client
	participant AuthController
	participant AppRepository
	participant Authenticator
	participant ShirtsController
	participant JWTAuth as 🔐 JWTAuthTokenFilterAttribute
	participant ClaimCheck as 🔒 RequiredClaimAttribute
	participant CreateFilter as 🧪 Shirt_ValidateCreateShirtFilter
	participant UpdateFilter as 🧪 Shirt_ValidateUpdateShirtFilter
	participant IdFilter as 🧪 Shirt_ValidateShirtIdFilter
	participant SizeCheck as 📏 Shirt_EnsureCorrectSizing
	participant Exception as 💥 ExceptionFilter
	participant Db as 🗄️ AppDbContext

	Client->>AuthController: POST /auth (ClientId + Secret)
	AuthController->>AppRepository: GetApplicationByClientId
	AppRepository-->>AuthController: Application + Scopes
	AuthController->>Authenticator: CreateToken(...)
	Authenticator-->>AuthController: JWT with claims
	AuthController-->>Client: 200 OK { Access_Token, Expires_At }

	Client->>Client: Add header:
	Note right of Client: Authorization: Bearer eyJ.hbG.cij...

	Client->>ShirtsController: GET /api/shirts/{id}
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("read", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>IdFilter: Check if shirt exists
	IdFilter->>Db: Find by ID
	alt Shirt found
		Db-->>IdFilter: Shirt object
		ShirtsController-->>Client: 200 OK
	else Missing
		Db-->>IdFilter: null
		IdFilter-->>Client: 404 Not Found
	end

	Note over Client, ShirtsController: 🛂 Every secured request requires sending the Bearer token in the Authorization header
```

## 📤 POST /api/shirts

```Mermaid
---
config:
	theme: redux-dark-color
---
sequenceDiagram
	autonumber
	title Shirts API

	participant Client
	participant AuthController
	participant AppRepository
	participant Authenticator
	participant ShirtsController
	participant JWTAuth as 🔐 JWTAuthTokenFilterAttribute
	participant ClaimCheck as 🔒 RequiredClaimAttribute
	participant CreateFilter as 🧪 Shirt_ValidateCreateShirtFilter
	participant UpdateFilter as 🧪 Shirt_ValidateUpdateShirtFilter
	participant IdFilter as 🧪 Shirt_ValidateShirtIdFilter
	participant SizeCheck as 📏 Shirt_EnsureCorrectSizing
	participant Exception as 💥 ExceptionFilter
	participant Db as 🗄️ AppDbContext

	Client->>AuthController: POST /auth (ClientId + Secret)
	AuthController->>AppRepository: GetApplicationByClientId
	AppRepository-->>AuthController: Application + Scopes
	AuthController->>Authenticator: CreateToken(...)
	Authenticator-->>AuthController: JWT with claims
	AuthController-->>Client: 200 OK { Access_Token, Expires_At }

	Client->>Client: Add header:
	Note right of Client: Authorization: Bearer eyJ.hbG.cij...

	Client->>ShirtsController: POST /api/shirts (Shirt)
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("write", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>CreateFilter: Validate for duplicate/null
	CreateFilter->>Db: Check duplicates
	alt Duplicate exists
		Db-->>CreateFilter: Match found
		CreateFilter-->>Client: 400 Bad Request
	else Valid
		Db-->>CreateFilter: No match
		ShirtsController->>SizeCheck: Validate size
		alt Invalid size
			SizeCheck-->>Client: 400 Bad Request
		else Valid
			SizeCheck-->>ShirtsController: ✅
			ShirtsController->>Db: Save new shirt
			Db-->>ShirtsController: ShirtId
			ShirtsController-->>Client: 201 Created
		end
	end

	Note over Client, ShirtsController: 🛂 Every secured request requires sending the Bearer token in the Authorization header
```

## ✏️ PUT /api/shirts/{id}

```Mermaid
---
config:
	theme: redux-dark-color
---
sequenceDiagram
	autonumber
	title Shirts API

	participant Client
	participant AuthController
	participant AppRepository
	participant Authenticator
	participant ShirtsController
	participant JWTAuth as 🔐 JWTAuthTokenFilterAttribute
	participant ClaimCheck as 🔒 RequiredClaimAttribute
	participant CreateFilter as 🧪 Shirt_ValidateCreateShirtFilter
	participant UpdateFilter as 🧪 Shirt_ValidateUpdateShirtFilter
	participant IdFilter as 🧪 Shirt_ValidateShirtIdFilter
	participant SizeCheck as 📏 Shirt_EnsureCorrectSizing
	participant Exception as 💥 ExceptionFilter
	participant Db as 🗄️ AppDbContext

	Client->>AuthController: POST /auth (ClientId + Secret)
	AuthController->>AppRepository: GetApplicationByClientId
	AppRepository-->>AuthController: Application + Scopes
	AuthController->>Authenticator: CreateToken(...)
	Authenticator-->>AuthController: JWT with claims
	AuthController-->>Client: 200 OK { Access_Token, Expires_At }

	Client->>Client: Add header:
	Note right of Client: Authorization: Bearer eyJ.hbG.cij...

	Client->>ShirtsController: PUT /api/shirts/{id}
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("write", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>IdFilter: Shirt exists?
	IdFilter->>Db: Find by ID
	alt Not found
		Db-->>Client: 404 Not Found
	else Found
		Db-->>ShirtsController: Shirt
		ShirtsController->>UpdateFilter: Compare route ID vs model ID
		alt ID mismatch
			UpdateFilter-->>Client: 400 Bad Request
		else Match
			ShirtsController->>SizeCheck: Validate size
			alt Invalid
				SizeCheck-->>Client: 400 Bad Request
			else Valid
				SizeCheck-->>Exception: Try SaveChanges
				Exception->>Db: Save
				Db-->>Exception: OK
				Exception-->>Client: 204 No Content
			end
		end
	end

	Note over Client, ShirtsController: 🛂 Every secured request requires sending the Bearer token in the Authorization header
```

## 🗑️ DELETE /api/shirts/{id}

```Mermaid
---
config:
	theme: redux-dark-color
---
sequenceDiagram
	autonumber
	title Shirts API

	participant Client
	participant AuthController
	participant AppRepository
	participant Authenticator
	participant ShirtsController
	participant JWTAuth as 🔐 JWTAuthTokenFilterAttribute
	participant ClaimCheck as 🔒 RequiredClaimAttribute
	participant CreateFilter as 🧪 Shirt_ValidateCreateShirtFilter
	participant UpdateFilter as 🧪 Shirt_ValidateUpdateShirtFilter
	participant IdFilter as 🧪 Shirt_ValidateShirtIdFilter
	participant SizeCheck as 📏 Shirt_EnsureCorrectSizing
	participant Exception as 💥 ExceptionFilter
	participant Db as 🗄️ AppDbContext

	Client->>AuthController: POST /auth (ClientId + Secret)
	AuthController->>AppRepository: GetApplicationByClientId
	AppRepository-->>AuthController: Application + Scopes
	AuthController->>Authenticator: CreateToken(...)
	Authenticator-->>AuthController: JWT with claims
	AuthController-->>Client: 200 OK { Access_Token, Expires_At }

	Client->>Client: Add header:
	Note right of Client: Authorization: Bearer eyJ.hbG.cij...

	Client->>ShirtsController: DELETE /api/shirts/{id}
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("delete", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>IdFilter: Validate ID exists
	IdFilter->>Db: Lookup
	alt Found
		Db-->>ShirtsController: Shirt
		ShirtsController->>Db: Delete shirt
		Db-->>ShirtsController: Deleted
		ShirtsController-->>Client: 200 OK
	else Not found
		Db-->>IdFilter: null
		IdFilter-->>Client: 404 Not Found
	end

	Note over Client, ShirtsController: 🛂 Every secured request requires sending the Bearer token in the Authorization header
```

## Consolidated Mermaid Chart with all endpoints

```Mermaid
---
config:
	theme: redux-dark-color
---
sequenceDiagram
	autonumber
	title Shirts API

	participant Client
	participant AuthController
	participant AppRepository
	participant Authenticator
	participant ShirtsController
	participant JWTAuth as 🔐 JWTAuthTokenFilterAttribute
	participant ClaimCheck as 🔒 RequiredClaimAttribute
	participant CreateFilter as 🧪 Shirt_ValidateCreateShirtFilter
	participant UpdateFilter as 🧪 Shirt_ValidateUpdateShirtFilter
	participant IdFilter as 🧪 Shirt_ValidateShirtIdFilter
	participant SizeCheck as 📏 Shirt_EnsureCorrectSizing
	participant Exception as 💥 ExceptionFilter
	participant Db as 🗄️ AppDbContext

	Client->>AuthController: POST /auth (ClientId + Secret)
	AuthController->>AppRepository: GetApplicationByClientId
	AppRepository-->>AuthController: Application + Scopes
	AuthController->>Authenticator: CreateToken(...)
	Authenticator-->>AuthController: JWT with claims
	AuthController-->>Client: 200 OK { Access_Token, Expires_At }

	Client->>Client: Add header:
	Note right of Client: Authorization: Bearer eyJ.hbG.cij...

	%% --- GET /api/shirts ---
	Client->>ShirtsController: GET /api/shirts
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("read", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>Db: Get all shirts
	Db-->>ShirtsController: List<Shirt>
	ShirtsController-->>Client: 200 OK

	%% --- GET /api/shirts/{id} ---
	Client->>ShirtsController: GET /api/shirts/{id}
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("read", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>IdFilter: Check if shirt exists
	IdFilter->>Db: Find by ID
	alt Shirt found
		Db-->>IdFilter: Shirt object
		ShirtsController-->>Client: 200 OK
	else Missing
		Db-->>IdFilter: null
		IdFilter-->>Client: 404 Not Found
	end

	%% --- POST /api/shirts ---
	Client->>ShirtsController: POST /api/shirts (Shirt)
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("write", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>CreateFilter: Validate for duplicate/null
	CreateFilter->>Db: Check duplicates
	alt Duplicate exists
		Db-->>CreateFilter: Match found
		CreateFilter-->>Client: 400 Bad Request
	else Valid
		Db-->>CreateFilter: No match
		ShirtsController->>SizeCheck: Validate size
		alt Invalid size
			SizeCheck-->>Client: 400 Bad Request
		else Valid
			SizeCheck-->>ShirtsController: ✅
			ShirtsController->>Db: Save new shirt
			Db-->>ShirtsController: ShirtId
			ShirtsController-->>Client: 201 Created
		end
	end

	%% --- PUT /api/shirts/{id} ---
	Client->>ShirtsController: PUT /api/shirts/{id}
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("write", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>IdFilter: Shirt exists?
	IdFilter->>Db: Find by ID
	alt Not found
		Db-->>Client: 404 Not Found
	else Found
		Db-->>ShirtsController: Shirt
		ShirtsController->>UpdateFilter: Compare route ID vs model ID
		alt ID mismatch
			UpdateFilter-->>Client: 400 Bad Request
		else Match
			ShirtsController->>SizeCheck: Validate size
			alt Invalid
				SizeCheck-->>Client: 400 Bad Request
			else Valid
				SizeCheck-->>Exception: Try SaveChanges
				Exception->>Db: Save
				Db-->>Exception: OK
				Exception-->>Client: 204 No Content
			end
		end
	end

	%% --- DELETE /api/shirts/{id} ---
	Client->>ShirtsController: DELETE /api/shirts/{id}
	ShirtsController->>JWTAuth: Validate token
	JWTAuth->>ClaimCheck: [RequiredClaim("delete", "true")]
	ClaimCheck-->>JWTAuth: ✅ OK
	ShirtsController->>IdFilter: Validate ID exists
	IdFilter->>Db: Lookup
	alt Found
		Db-->>ShirtsController: Shirt
		ShirtsController->>Db: Delete shirt
		Db-->>ShirtsController: Deleted
		ShirtsController-->>Client: 200 OK
	else Not found
		Db-->>IdFilter: null
		IdFilter-->>Client: 404 Not Found
	end

	Note over Client, ShirtsController: 🛂 Every secured request requires sending the Bearer token in the Authorization header
```