/*
Üksfaililine React-komponent: üks-lehe e-pood (nimi: Anti-Drug Cup Lids)
Kasutusjuhend:
1) Loo Vite React projekt ja asetada see fail `src/LandingPage.jsx` või impordi App-sse.
2) See komponent sisaldab: tootelehe, tootekataloogi, ostukorvi ja "Checkout" nuppu, mis avab `mailto:`-põhise tellimuse e-kirja (ei vaja backend'i).
3) Asenda pildi URLid oma piltidega, või saada mulle pildid, siis lisan need.
4) Kui soovid reaalset makset (Stripe, PayPal, Netlify Forms jms), saan selle hiljem lisada.

Märkus: see kood on kujundatud Tailwindiga, aga töötab ka lihtsama CSS-iga — kui Tailwind puudub, lisa minimaalsed klassid või ütle, lisan eraldi CSS.
*/

import React, { useState } from 'react';

const PRODUCTS = [
  {
    id: 'lid-anti-drug-001',
    title: 'Anti-Drug Topsikaas (Cup Lid)',
    price: 14.90,
    short: 'Turvaline topsikaas, mis vähendab riski, et keegi midagi topsi sisse lisab.',
    description:
      'Reguleeritav, tihedalt kinnituv topsikaas — mõeldud baridele, festivalide ja igapäevaseks kasutamiseks. Valmistatud vastupidavast plastikust, lihtne puhastada. Sobib enamikule standardsetele papp- ja plasttopsidele.',
    variants: ['Must', 'Valge'],
    image: 'https://via.placeholder.com/800x600?text=Anti-Drug+Topsikaas',
  },
];

export default function LandingPage() {
  const [cart, setCart] = useState([]);

  function addToCart(product, variant, qty = 1) {
    setCart((c) => {
      const existing = c.find((i) => i.id === product.id && i.variant === variant);
      if (existing) {
        return c.map((i) => (i.id === product.id && i.variant === variant ? { ...i, qty: i.qty + qty } : i));
      }
      return [...c, { id: product.id, title: product.title, price: product.price, variant, qty }];
    });
  }

  function updateQty(item, newQty) {
    setCart((c) => c.map((i) => (i.id === item.id && i.variant === item.variant ? { ...i, qty: newQty } : i)).filter(i => i.qty > 0));
  }

  function removeItem(item) {
    setCart((c) => c.filter((i) => !(i.id === item.id && i.variant === item.variant)));
  }

  function cartTotal() {
    return cart.reduce((s, i) => s + i.price * i.qty, 0);
  }

  function checkoutMailto() {
    if (cart.length === 0) return;
    const to = 'info@example.com';
    const subject = encodeURIComponent('Tellimus: Anti-Drug Topsikaasid');
    const lines = cart.map((i, idx) => `${idx + 1}. ${i.title} (${i.variant}) — ${i.qty} x €${i.price.toFixed(2)} = €${(i.qty * i.price).toFixed(2)}`);
    lines.push(`
Kokku: €${cartTotal().toFixed(2)}`);
    const body = encodeURIComponent(`Tere!

Soovin tellida alljärgnevad tooted:

${lines.join('
')}

Tarneinfo:
Nimi:
E-post:
Telefon:
Aadress (valikuline):

Lisainfo:`);
    window.location.href = `mailto:${to}?subject=${subject}&body=${body}`;
  }

  return (
    <div className="min-h-screen bg-gray-50 font-sans text-gray-800">
      <header className="bg-white shadow-sm">
        <div className="max-w-6xl mx-auto px-6 py-4 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="w-10 h-10 rounded-lg bg-gradient-to-br from-indigo-500 to-purple-500 flex items-center justify-center text-white font-bold">V</div>
            <div>
              <div className="font-semibold">Veebipood: Anti-Drug Topsikaasid</div>
              <div className="text-xs text-gray-500">Turvalisus ja ennetus — topsikaas, mis kaitseb joogi sisu</div>
            </div>
          </div>
          <div className="flex items-center gap-4">
            <a href="#products" className="text-sm hover:underline">Tooted</a>
            <a href="#contact" className="text-sm hover:underline">Kontakt</a>
            <button
              onClick={() => { const el = document.getElementById('cart-panel'); if (el) el.scrollIntoView({ behavior: 'smooth' }); }}
              className="px-4 py-2 rounded-lg border"
            >
              Ostukorv ({cart.reduce((s, i) => s + i.qty, 0)})
            </button>
          </div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-6 py-10">
        <section className="grid md:grid-cols-2 gap-8 items-center">
          <div>
            <h1 className="text-3xl font-extrabold">Anti-Drug Topsikaas — turvalisus sinu joogile</h1>
            <p className="mt-4 text-gray-600">Mõeldud selleks, et vähendada võimalust, et keegi topsi sisse midagi lisab. Sobib üritustele, baaridele, kohvikutele ja erakasutuseks.</p>

            <ul className="mt-6 text-sm text-gray-700 space-y-2">
              <li>• Tihe ja turvaline sulgemine</li>
              <li>• Sobib standardsetele topsidele</li>
              <li>• Korduvkasutatav ja kergesti puhastatav</li>
              <li>• Valikus erinevad värvid</li>
            </ul>

            <div className="mt-6 flex gap-3">
              <a href="#products" className="px-5 py-3 rounded-lg bg-indigo-600 text-white">Vaata toodet</a>
              <a href="#contact" className="px-5 py-3 rounded-lg border">Võta ühendust</a>
            </div>
          </div>

          <div>
            <img src={PRODUCTS[0].image} alt="Anti-Drug Topsikaas" className="w-full h-80 object-cover rounded-lg shadow" />
            <p className="mt-2 text-xs text-gray-500">Pilt asendamiseks: saada mulle pildid ja lisan.</p>
          </div>
        </section>

        <section id="products" className="mt-12">
          <h2 className="text-2xl font-semibold">Tootekataloog</h2>
          <div className="mt-6 grid sm:grid-cols-2 lg:grid-cols-3 gap-6">
            {PRODUCTS.map((p) => (
              <ProductCard key={p.id} product={p} onAdd={addToCart} />
            ))}
          </div>
        </section>

        <section id="cart-panel" className="mt-12 bg-white rounded-lg p-6 shadow">
          <h3 className="text-xl font-semibold">Ostukorv</h3>
          {cart.length === 0 ? (
            <p className="mt-4 text-gray-600">Ostukorv on tühi.</p>
          ) : (
            <div className="mt-4">
              <div className="space-y-3">
                {cart.map((item, idx) => (
                  <div key={`${item.id}-${item.variant}`} className="flex items-center justify-between p-3 border rounded">
                    <div>
                      <div className="font-semibold">{item.title}</div>
                      <div className="text-sm text-gray-500">{item.variant}</div>
                    </div>
                    <div className="flex items-center gap-3">
                      <input type="number" min={1} value={item.qty} onChange={(e) => updateQty(item, Math.max(1, Number(e.target.value)))} className="w-16 px-2 py-1 border rounded" />
                      <div className="w-24 text-right">€{(item.price * item.qty).toFixed(2)}</div>
                      <button onClick={() => removeItem(item)} className="px-3 py-1 border rounded">Eemalda</button>
                    </div>
                  </div>
                ))}
              </div>

              <div className="mt-4 flex items-center justify-between">
                <div className="text-lg font-semibold">Kokku: €{cartTotal().toFixed(2)}</div>
                <div className="flex gap-3">
                  <button onClick={() => setCart([])} className="px-4 py-2 border rounded">Tühjenda</button>
                  <button onClick={checkoutMailto} className="px-4 py-2 rounded-lg bg-green-600 text-white">Tellimus e-kirjaga</button>
                </div>
              </div>
            </div>
          )}
        </section>

        <section id="contact" className="mt-12">
          <h2 className="text-2xl font-semibold">Kontakt</h2>
          <p className="mt-2 text-gray-600">Kui soovid, et lisan tootevariatsioone, hindasid või makselahenduse (Stripe/PayPal), anna teada.</p>

          <form className="mt-4 grid sm:grid-cols-2 gap-4" onSubmit={(e) => { e.preventDefault(); window.location.href = `mailto:info@example.com?subject=${encodeURIComponent('Päring veebipoe kohta')}&body=${encodeURIComponent('Tere!

Kirjeldus: ')}`; }}>
            <input placeholder="Nimi" className="px-4 py-3 rounded border" />
            <input type="email" placeholder="E-post" className="px-4 py-3 rounded border" />
            <textarea placeholder="Sõnum" className="px-4 py-3 rounded border col-span-2" rows={4}></textarea>
            <div className="col-span-2 flex gap-3">
              <button type="submit" className="px-6 py-3 rounded bg-indigo-600 text-white">Saada</button>
              <button type="button" className="px-6 py-3 rounded border" onClick={() => alert('Lisa pildid mulle — ma asendan pildid komponendis')}>Saada pildid</button>
            </div>
          </form>
        </section>
      </main>

      <footer className="bg-white border-t py-6">
        <div className="max-w-6xl mx-auto px-6 text-sm text-gray-600">© {new Date().getFullYear()} Anti-Drug Topsikaasid — Kõik õigused kaitstud</div>
      </footer>
    </div>
  );
}

function ProductCard({ product, onAdd }) {
  const [variant, setVariant] = useState(product.variants?.[0] ?? null);
  const [qty, setQty] = useState(1);
  return (
    <div className="bg-white rounded-lg shadow p-4 flex flex-col">
      <img src={product.image} alt={product.title} className="w-full h-40 object-cover rounded" />
      <div className="mt-3 flex-1">
        <h4 className="font-semibold">{product.title}</h4>
        <p className="text-sm text-gray-600 mt-1">{product.short}</p>
        <div className="mt-3 text-sm">€{product.price.toFixed(2)}</div>
      </div>
      {product.variants && (
        <select value={variant} onChange={(e) => setVariant(e.target.value)} className="mt-3 px-3 py-2 border rounded">
          {product.variants.map((v) => (
            <option key={v} value={v}>{v}</option>
          ))}
        </select>
      )}
      <div className="mt-3 flex items-center gap-3">
        <input type="number" min={1} value={qty} onChange={(e) => setQty(Math.max(1, Number(e.target.value)))} className="w-20 px-2 py-2 border rounded" />
        <button onClick={() => onAdd(product, variant, qty)} className="ml-auto px-4 py-2 rounded bg-indigo-600 text-white">Lisa ostukorvi</button>
      </div>
    </div>
  );
}
