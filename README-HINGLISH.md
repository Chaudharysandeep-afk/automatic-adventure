# NovaCart Secure Admin — setup guide (Hinglish)

Ye starter tumhare current static demo ko ek backend-backed storefront mein badalne ke liye hai. Isme Cloudflare Worker API, D1 database, password hashing, HttpOnly session cookie aur product add/edit/delete API hai. Ye abhi tumhare live site par automatically install nahi hua hai.

## Zaroori baat
- Sirf static HTML/JS mein password rakhna secure nahi hota. Is version mein password browser code mein store nahi hota.
- Real customer payments/order checkout is starter mein included nahi hai.
- Production mein use karne se pehle deployment ko test karo aur strong unique passwords rakho.

## Cloudflare setup (desktop/phone browser; Git-based deploy easiest)
1. Cloudflare dashboard → Workers & Pages → D1 SQL Database → Create database. Naam `novacart-db` rakh sakte ho.
2. D1 database ke Console/SQL section mein `schema.sql` ka poora SQL run karo.
3. D1 database ka ID copy karo. `wrangler.jsonc` mein `REPLACE_WITH_YOUR_D1_DATABASE_ID` ki jagah apna ID lagao.
4. Project ko GitHub repository mein upload karo (ZIP extract karke files upload karo). Private repo rakhna behtar hai.
5. Cloudflare → Workers & Pages → Create application → connect Git repository, is repo ko select karo. Build/deploy command `npx wrangler deploy` use karo; project root wahi hona chahiye jahan `wrangler.jsonc` hai. Static assets `public/` folder mein hain.
6. Deployed Worker → Settings → Variables and Secrets → Add secret. Name `SETUP_KEY`, value ek long random phrase jo kisi aur ko na pata ho. Save and redeploy if Cloudflare asks.
7. Deployment ke baad apni site par `/setup.html` kholo. One-time setup key, admin username aur ek unique strong password (12+ characters) enter karke Create admin account dabao. Setup key/password kisi ke saath share mat karna.
8. Admin create hote hi Cloudflare Worker Settings → Variables and Secrets se `SETUP_KEY` secret delete kar do. `/api/setup` first admin ke baad khud bhi band ho jata hai.

## Bindings and deployment
`wrangler.jsonc` includes the D1 binding named `DB` and assets binding named `ASSETS`. Make sure the deployed Worker has the D1 database binding named exactly `DB` pointing at `novacart-db`. Add `SETUP_KEY` only as a Secret, never commit it to GitHub.

## Routes
- `GET /api/products` — public product list
- `POST /api/login` — admin login
- `POST /api/logout` — sign out
- `GET /api/me` — session status
- `POST /api/products` — add product (admin only)
- `PUT /api/products/:id` — edit product (admin only)
- `DELETE /api/products/:id` — delete product (admin only)
- `POST /api/setup` — creates the first admin only

If these steps feel too technical on Android, do not paste passwords or setup secrets into chat. Send a screenshot of the Cloudflare screen and I can guide one step at a time.
