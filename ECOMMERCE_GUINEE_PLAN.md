# LaGuinèmakiti — Plan produit (Marketplace Guinée)

## 1) Concept (simple et clair)
**LaGuinèmakiti** est une marketplace mobile “Pinterest + Jumia” pensée pour la Guinée :
- les vendeurs créent leur boutique,
- publient des produits (photos, prix, description),
- les clients découvrent via un feed visuel,
- ajoutent au panier,
- commandent et paient selon les moyens locaux.

## 2) Objectifs business
- Digitaliser le commerce local en facilitant la vente en ligne.
- Créer un environnement de confiance (anti-arnaque).
- S’adapter aux habitudes de paiement guinéennes.
- Générer un revenu plateforme via commission sur ventes.

---

## 3) Rôles utilisateurs

### A. Client
- Créer un compte.
- Explorer les produits (feed + recherche + filtres).
- Ajouter aux favoris et au panier.
- Commander et payer.
- Suivre ses commandes.
- Noter les vendeurs/produits.

### B. Vendeur
- Créer son compte vendeur et sa boutique.
- Ajouter/éditer ses produits.
- Voir et traiter les commandes.
- Recevoir sa part automatiquement (split payment).
- Accéder à des outils IA (description + amélioration photo).

### C. Admin (plateforme)
- Vérifier les vendeurs.
- Modérer contenus/produits frauduleux.
- Gérer commissions.
- Gérer litiges/remboursements.
- Suivre KPI (ventes, conversion, rétention).

---

## 4) Fonctionnalités MVP (Phase 1)

### Authentification
- Inscription/connexion Client.
- Inscription/connexion Vendeur.
- Gestion de profil (nom, téléphone, ville, avatar).

### Marketplace
- Feed visuel (style Pinterest, scroll infini).
- Page produit détaillée (images, prix, description, vendeur, stock).
- Recherche + filtres (prix, catégorie, ville, disponibilité).
- Favoris (❤️).

### Côté vendeur
- Création de boutique.
- Ajout produit (photo, nom, prix, description, stock).
- Gestion catalogue (actif/inactif, stock).

### Panier & commande
- Panier multi-vendeurs.
- Modification quantité/suppression articles.
- Création de commande.
- Historique des commandes client/vendeur.
- Statuts: `pending`, `confirmed`, `shipped`, `delivered`, `cancelled`.

### WhatsApp (important)
- Bouton “Contacter sur WhatsApp”.
- Message pré-rempli (produit, quantité, total, référence).

### Paiement initial de confiance
- Paiement à la livraison (Cash on Delivery) activé au lancement.

---

## 5) Fonctionnalités Phase 2 (monétisation locale)

### Paiements intégrés
- Intégration **Orange Money**.
- Intégration **MTN MoMo**.
- Option complémentaire: **CinetPay / PayDunya** comme agrégateurs.

### Commission plateforme
- Taux de commission paramétrable (ex: 5% à 12%).
- Calcul automatique à la commande.
- Reporting vendeur (montant brut, commission, net).

### Split payment automatique
- À chaque paiement validé:
  - part plateforme prélevée automatiquement,
  - part vendeur versée automatiquement.
- Journal comptable par transaction.

---

## 6) IA à intégrer

### A. Recommandation produits (client)
- Suggestions personnalisées selon:
  - clics,
  - favoris,
  - historique commandes,
  - produits similaires.
- Objectif: augmenter le taux de conversion.

### B. Génération de descriptions (vendeur)
- Le vendeur saisit un titre court.
- L’IA propose une description commerciale propre, claire et vendeuse.

### C. Amélioration d’images
- Nettoyage visuel automatique:
  - luminosité,
  - contraste,
  - recadrage,
  - fond plus propre.

---

## 7) Confiance & sécurité (obligatoire)
- Vérification vendeurs (KYC léger: téléphone + pièce + infos boutique).
- Badge “Vendeur vérifié”.
- Système d’avis/notes ⭐.
- Détection d’annonces suspectes (prix anormal, contenu frauduleux).
- Signalement produit/vendeur par les clients.
- Logs d’audit admin.

---

## 8) Écrans principaux (UX)
1. **Accueil**: feed mosaïque visuel + scroll infini.
2. **Recherche**: barre + filtres avancés.
3. **Page produit**: visuels, prix, vendeur, avis, CTA acheter/WhatsApp.
4. **Panier**: liste produits, frais, total, bouton commander.
5. **Checkout**: adresse, mode de livraison, mode de paiement.
6. **Profil client**: commandes, favoris, paramètres.
7. **Dashboard vendeur**: catalogue, commandes, revenus.
8. **Console admin**: vérification, modération, métriques.

---

## 9) Base de données propre (version évolutive)

### Entités clés
- `users`
- `shops`
- `seller_verifications`
- `categories`
- `products`
- `product_images`
- `favorites`
- `carts`
- `cart_items`
- `orders`
- `order_items`
- `payments`
- `payouts`
- `commissions`
- `reviews`
- `shipments`
- `ai_recommendation_events`

### Exemple de tables paiement
- `payments` : provider, amount, status, external_txn_id.
- `commissions` : order_id, rate, platform_amount, seller_amount.
- `payouts` : seller_id, payment_id, amount, status.

---

## 10) Architecture technique recommandée
- **Mobile app**: React Native (ou Flutter).
- **Backend API**: Node.js (NestJS/Express).
- **DB**: PostgreSQL.
- **Cache/queue**: Redis + BullMQ.
- **Stockage images**: Cloudinary ou S3.
- **Recherche**: PostgreSQL full text (puis Elastic si scale).
- **IA**:
  - service de génération texte,
  - service de recommandation,
  - pipeline traitement image.
- **Déploiement**: Render / Railway / AWS.

---

## 11) API minimale (MVP + extension paiements)
- `POST /auth/register-client`
- `POST /auth/register-seller`
- `POST /auth/login`
- `POST /shops`
- `POST /products`
- `GET /products`
- `GET /products/:id`
- `POST /cart/items`
- `PATCH /cart/items/:id`
- `DELETE /cart/items/:id`
- `POST /orders/from-cart`
- `GET /orders/me`
- `POST /orders/:id/whatsapp-link`
- `POST /payments/initiate`
- `POST /payments/webhook/:provider`
- `POST /orders/:id/reviews`

---

## 12) Roadmap de mise en œuvre

### Phase 0 — Cadrage (1–2 semaines)
- UX wireframes.
- Schéma DB final.
- Spécification API.

### Phase 1 — MVP sans paiement auto (4–6 semaines)
- Comptes client/vendeur.
- Boutique + produits.
- Feed + recherche.
- Panier + commande.
- WhatsApp + paiement à la livraison.

### Phase 2 — Paiements locaux & revenus (3–4 semaines)
- Orange Money + MTN MoMo.
- Commission plateforme.
- Split payment.

### Phase 3 — IA & confiance avancée (3–5 semaines)
- Recommandations personnalisées.
- Génération de descriptions.
- Amélioration image.
- Scoring anti-fraude.

---

## 13) KPI à suivre
- GMV mensuel.
- Nombre de vendeurs actifs.
- Taux conversion visite → commande.
- Panier moyen.
- Taux de livraison réussie.
- Taux de litiges/remboursements.
- Réachat à 30 jours.

---

## 14) Décision produit recommandée
Commencer **simple et fiable**:
1. MVP avec WhatsApp + paiement à la livraison,
2. puis paiements Orange Money / MTN MoMo,
3. puis IA pour accélérer la croissance.

Cette stratégie réduit le risque, construit la confiance locale, et permet d’atteindre rapidement un produit utilisable en Guinée.
