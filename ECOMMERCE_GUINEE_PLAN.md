# Application e-commerce pour la Guinée (sans paiement automatique)

## Vision
Créer une marketplace visuelle (inspirée de Pinterest/Shine) où :
- des vendeurs créent une boutique,
- publient des produits (articles, vêtements, etc.),
- des clients découvrent les offres,
- ajoutent au panier,
- passent commande,
- finalisent via WhatsApp (sans paiement intégré).

## Fonctionnalités demandées

### 1) Comptes & rôles
- Inscription/connexion **Client**.
- Inscription/connexion **Vendeur**.
- Profil utilisateur (nom, téléphone, ville, photo facultative).
- Vérification basique vendeur (numéro WhatsApp + identité commerciale).

### 2) Espace vendeur
- Création de boutique (nom, description, logo, localisation).
- Ajout / modification / suppression de produits.
- Gestion du stock et statut (disponible, rupture).
- Galerie d’images produit.

### 3) Catalogue client
- Fil visuel type mosaïque.
- Recherche par mot-clé.
- Filtres : catégorie, prix, ville, disponibilité.
- Détail produit (photos, prix, vendeur, description, stock).

### 4) Panier (ajout demandé)
- Ajouter/supprimer produit.
- Modifier quantités.
- Calcul sous-total par vendeur.
- Un panier peut contenir des articles de plusieurs vendeurs.

### 5) Commande (ajout demandé)
- Création d’une commande à partir du panier.
- Statuts : `pending`, `confirmed`, `shipped`, `delivered`, `cancelled`.
- Historique des commandes côté client et vendeur.
- Pas de paiement automatique ; commande confirmée via WhatsApp.

### 6) Commande via WhatsApp (obligatoire)
- Bouton **Commander sur WhatsApp**.
- Génération automatique d’un message pré-rempli (produits, quantités, total estimatif, référence commande).
- Redirection vers `wa.me/<numero_vendeur>?text=<message_encode>`.

## Base de données propre (ajout demandé)

### Entités principales
- `users` (client/vendeur/admin)
- `shops`
- `categories`
- `products`
- `product_images`
- `carts`
- `cart_items`
- `orders`
- `order_items`
- `order_events` (historique de statut)

### Schéma SQL (PostgreSQL recommandé)
```sql
create table users (
  id uuid primary key,
  role varchar(20) not null check (role in ('client','seller','admin')),
  full_name varchar(120) not null,
  email varchar(190) unique,
  phone varchar(30) unique not null,
  password_hash text not null,
  city varchar(80),
  created_at timestamptz not null default now()
);

create table shops (
  id uuid primary key,
  owner_id uuid not null references users(id) on delete cascade,
  name varchar(140) not null,
  slug varchar(160) unique not null,
  description text,
  whatsapp_number varchar(30) not null,
  city varchar(80),
  created_at timestamptz not null default now()
);

create table products (
  id uuid primary key,
  shop_id uuid not null references shops(id) on delete cascade,
  title varchar(180) not null,
  description text,
  price_gnf bigint not null check (price_gnf >= 0),
  stock int not null default 0,
  is_active boolean not null default true,
  created_at timestamptz not null default now()
);

create table carts (
  id uuid primary key,
  user_id uuid not null references users(id) on delete cascade,
  status varchar(20) not null default 'active' check (status in ('active','converted')),
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);

create table cart_items (
  id uuid primary key,
  cart_id uuid not null references carts(id) on delete cascade,
  product_id uuid not null references products(id),
  quantity int not null check (quantity > 0),
  unit_price_gnf bigint not null check (unit_price_gnf >= 0),
  unique(cart_id, product_id)
);

create table orders (
  id uuid primary key,
  user_id uuid not null references users(id),
  order_ref varchar(40) unique not null,
  status varchar(20) not null default 'pending'
    check (status in ('pending','confirmed','shipped','delivered','cancelled')),
  total_gnf bigint not null check (total_gnf >= 0),
  whatsapp_sent boolean not null default false,
  created_at timestamptz not null default now()
);

create table order_items (
  id uuid primary key,
  order_id uuid not null references orders(id) on delete cascade,
  product_id uuid not null references products(id),
  seller_id uuid not null references users(id),
  quantity int not null check (quantity > 0),
  unit_price_gnf bigint not null check (unit_price_gnf >= 0)
);
```

## API minimale (MVP)
- `POST /auth/register-client`
- `POST /auth/register-seller`
- `POST /auth/login`
- `POST /shops`
- `POST /products`
- `GET /products`
- `GET /products/:id`
- `POST /cart/items`
- `PATCH /cart/items/:itemId`
- `DELETE /cart/items/:itemId`
- `POST /orders/from-cart`
- `GET /orders/me`
- `POST /orders/:id/whatsapp-link`

## Stack conseillée
- Frontend : React/Next.js (UI visuelle type cards/masonry).
- Backend : Node.js + Express.
- DB : PostgreSQL + Prisma/Sequelize.
- Stockage images : Cloudinary ou S3.
- Déploiement : Render/Railway/Fly.

## Parcours utilisateur
1. Client crée son compte.
2. Explore le feed, ouvre un produit.
3. Ajoute au panier.
4. Valide le panier → création commande.
5. Clique “Commander sur WhatsApp” pour contacter le vendeur avec message pré-rempli.

## Contraintes importantes
- **Aucun paiement automatique** dans le MVP.
- Traçabilité complète des commandes en base.
- Protection anti-spam (rate limiting) et validation stricte des entrées.
- Journalisation et sauvegarde régulière de la base.
