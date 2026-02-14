<!DOCTYPE html>
<html lang="pl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>System faktur RP z loginem</title>
<style>
    body {
        margin: 0;
        font-family: Arial, sans-serif;
        background: #000; /* całkowicie czarne tło przed zalogowaniem */
        color: #fff;
    }

    /* MODAL LOGOWANIA */
    #passwordModal {
        position: fixed;
        inset: 0;
        background: #000;
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }

    #modalContent {
        background: #1a1a1a;
        padding: 40px;
        border-radius: 20px;
        text-align: center;
        border: 2px solid #ffd700;
    }

    #modalContent input {
        padding: 12px;
        font-size: 16px;
        border-radius: 10px;
        border: 1px solid #ffd700;
        margin-top: 10px;
        width: 200px;
        background: #000;
        color: #fff;
    }

    #modalContent button {
        margin-top: 15px;
        padding: 12px 20px;
        border: none;
        border-radius: 12px;
        background: #ffd700;
        color: #000;
        font-weight: bold;
        cursor: pointer;
    }

    #modalContent button:hover {
        background: #e6c200;
    }

    #wrongPassword {
        color: red;
        margin-top: 10px;
        display: none;
    }

    /* GŁÓWNA TREŚĆ STRONY (UKRYTA NA START) */
    #mainContent {
        display: none; /* ukryte przed zalogowaniem */
        background: #070707;
        min-height: 100vh;
        padding: 20px;
    }

    .logoutBtn {
        padding: 10px 15px;
        border: none;
        border-radius: 10px;
        background: #ff0000;
        color: #fff;
        cursor: pointer;
        margin-bottom: 15px;
    }

    .logoutBtn:hover {
        background: #cc0000;
    }

    /* Role display */
    #roleDisplay {
        margin-bottom: 20px;
        font-weight: bold;
        color: #ffd700;
    }

    /* --- Formularz faktury --- */
    .container {
        max-width: 1400px;
        margin: 20px auto;
        padding: 20px;
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 20px;
    }

    .panel {
        background: #1a1a1a;
        border-radius: 20px;
        padding: 18px;
        border: 1px solid rgba(255, 215, 0, 0.35);
        box-shadow: 0 0 30px rgba(255, 215, 0, 0.25);
    }

    .title {
        text-align: center;
        font-size: 30px;
        font-weight: bold;
        color: #ffd700;
        margin-bottom: 10px;
        text-shadow: 0 0 25px rgba(255, 215, 0, 0.9);
    }

    .subtitle {
        text-align: center;
        color: #cfcfcf;
        margin-bottom: 18px;
        font-size: 14px;
    }

    .section {
        margin-bottom: 18px;
        padding: 14px;
        border-radius: 16px;
        background: rgba(255,255,255,0.05);
        border: 1px solid rgba(255, 215, 0, 0.2);
    }

    .section h2 {
        font-size: 18px;
        color: #ffd700;
        margin: 0 0 10px;
    }

    .field {
        display: flex;
        flex-direction: column;
        gap: 5px;
        margin-bottom: 10px;
    }

    .field label {
        font-size: 16px;
        color: #ffd700;
        font-weight: bold;
    }

    .field input, .field textarea {
        padding: 12px;
        font-size: 15px;
        border-radius: 10px;
        border: 1px solid rgba(255, 215, 0, 0.35);
        background: rgba(0,0,0,0.65);
        color: #fff;
        outline: none;
        transition: all 0.3s ease;
    }

    .field textarea {
        resize: vertical;
    }

    .field input:focus, .field textarea:focus {
        box-shadow: 0 0 60px rgba(255, 215, 0, 1);
        border-color: rgba(255, 215, 0, 0.9);
    }

    .accordion {
        border-radius: 14px;
        border: 1px solid rgba(255, 215, 0, 0.25);
        background: rgba(255,255,255,0.04);
        margin-bottom: 10px;
    }

    .accordion summary {
        cursor: pointer;
        padding: 12px;
        font-size: 16px;
        font-weight: bold;
        color: #fff;
        list-style: none;
        background: rgba(0,0,0,0.45);
        border-bottom: 1px solid rgba(255, 215, 0, 0.25);
    }

    .accordion summary::-webkit-details-marker {
        display: none;
    }

    .accordion .items {
        padding: 10px;
        display: grid;
        grid-template-columns: 1fr;
        gap: 8px;
    }

    .item {
        display: flex;
        align-items: flex-start;
        gap: 12px;
        padding: 10px;
        border-radius: 12px;
        border: 1px solid rgba(255, 215, 0, 0.2);
        background: rgba(0,0,0,0.35);
        transition: all 0.2s ease;
    }

    .item:hover {
        box-shadow: 0 0 50px rgba(255, 215, 0, 0.9);
        transform: translateY(-2px);
    }

    .item input {
        transform: scale(1.2);
        margin-top: 3px;
    }

    .item .name {
        font-size: 15px;
        font-weight: bold;
    }

    .item .price {
        font-size: 14px;
        color: #ffd700;
        margin-top: 3px;
    }

    .total {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-top: 10px;
        padding: 12px;
        border-radius: 14px;
        border: 1px solid rgba(255, 215, 0, 0.25);
        background: rgba(0,0,0,0.4);
        font-size: 18px;
        font-weight: bold;
    }

    .sendBtn {
        padding: 14px;
        font-size: 18px;
        font-weight: bold;
        color: #000;
        background: #ffd700;
        border: none;
        border-radius: 16px;
        cursor: pointer;
        transition: all 0.3s ease;
        margin-top: 15px;
        width: 100%;
    }

    .sendBtn:hover {
        transform: translateY(-2px);
        box-shadow: 0 0 60px rgba(255, 215, 0, 0.9);
    }

    /* PRZYCISK WYCZYŚĆ */
    .clearBtn {
        padding: 14px;
        font-size: 18px;
        font-weight: bold;
        color: #fff;
        background: #333;
        border: 1px solid rgba(255, 215, 0, 0.35);
        border-radius: 16px;
        cursor: pointer;
        transition: all 0.3s ease;
        margin-top: 10px;
        width: 100%;
    }

    .clearBtn:hover {
        transform: translateY(-2px);
        box-shadow: 0 0 60px rgba(255, 215, 0, 0.6);
    }

    /* PREVIEW */
    .preview {
        padding: 18px;
        border-radius: 18px;
        background: linear-gradient(135deg, #1c1c1c, #0b0b0b);
        border: 1px solid rgba(255, 215, 0, 0.3);
        box-shadow: 0 0 30px rgba(255, 215, 0, 0.25);
        height: 650px;
        overflow: auto;
        position: relative;
    }

    /* Papierowy efekt + promienie (5% i bardziej w lewo) */
    .preview::before {
        content: "";
        position: absolute;
        inset: 0;
        background: 
            repeating-linear-gradient(
                0deg,
                rgba(255,255,255,0.05),
                rgba(255,255,255,0.05) 1px,
                rgba(0,0,0,0) 1px,
                rgba(0,0,0,0) 8px
            ),
            radial-gradient(circle at 0% 10%, rgba(255, 215, 0, 0.05), rgba(0,0,0,0) 5%),
            radial-gradient(circle at 20% 40%, rgba(255, 215, 0, 0.03), rgba(0,0,0,0) 5%);
        border-radius: 18px;
    }

    .previewContent {
        position: relative;
        z-index: 2;
        padding: 10px;
    }

    .preview h3 {
        margin: 0;
        font-size: 22px;
        color: #ffd700;
    }

    .preview p {
        margin: 6px 0;
        font-size: 14px;
        color: #fff;
        line-height: 1.3;
    }

    .line {
        height: 1px;
        background: rgba(255, 215, 0, 0.4);
        margin: 10px 0;
    }

    .servicesList {
        white-space: pre-line;
        font-size: 14px;
        max-height: 220px;
        overflow: auto;
    }
</style>
</head>
<body>

<!-- MODAL LOGOWANIA -->
<div id="passwordModal">
    <div id="modalContent">
        <h2>Wprowadź dane logowania</h2>
        <input type="text" id="usernameInput" placeholder="Login">
        <input type="password" id="passwordInput" placeholder="Hasło">
        <button onclick="checkLogin()">Zaloguj</button>
        <p id="wrongPassword">Błędne dane!</p>
    </div>
</div>

<!-- GŁÓWNA TREŚĆ STRONY -->
<div id="mainContent">
    <button class="logoutBtn" onclick="logout()">Wyloguj</button>
    <p id="roleDisplay"></p>

    <!-- Formularz faktury -->
    <div class="container">
        <div class="panel">
            <div class="title">Faktura</div>
            <div class="subtitle">Wypełnij formularz, wybierz usługi i wyślij</div>

            <!-- Dane wystawiającego -->
            <div class="section">
                <h2>Dane wystawiającego</h2>
                <div class="field">
                    <label>Nick (Discord)</label>
                    <input id="issuerNick" placeholder="np. KasFuries#1234">
                </div>
                <div class="field">
                    <label>Imię i nazwisko (IC)</label>
                    <input id="issuerIC" placeholder="np. Jan Kowalski">
                </div>
            </div>

            <!-- Dane klienta -->
            <div class="section">
                <h2>Dane klienta</h2>
                <div class="field">
                    <label>Imię i nazwisko klienta</label>
                    <input id="clientName" placeholder="np. Adam Nowak">
                </div>
                <div class="field">
                    <label>Nick klienta (Discord)</label>
                    <input id="clientNick" placeholder="np. AdamNowak#1234">
                </div>
            </div>

            <!-- Szczegóły zdarzenia -->
            <div class="section">
                <h2>Szczegóły zdarzenia</h2>
                <div class="field">
                    <label>Zakres świadczenia</label>
                    <textarea id="scope" rows="3" placeholder="Np. holowanie i zabezpieczenie miejsca..."></textarea>
                </div>
                <div class="field">
                    <label>Miejsce zdarzenia</label>
                    <input id="place" placeholder="np. Autostrada A1, 12km">
                </div>
                <div class="field">
                    <label>Data i godzina</label>
                    <input id="dateTime" placeholder="np. 2026-01-20 22:30">
                </div>
            </div>

            <!-- Pojazd -->
            <div class="section">
                <h2>Dane pojazdu</h2>
                <div class="field">
                    <label>Rodzaj pojazdu</label>
                    <input id="vehicleType" placeholder="np. samochód osobowy / motocykl">
                </div>
                <div class="field">
                    <label>Rejestracja</label>
                    <input id="carPlate" placeholder="np. WAW 12345">
                </div>
            </div>

            <div class="section">
            <h2>Usługi (cennik)</h2>

            <div class="accordion">
                <details>
                    <summary>Naprawy</summary>
                    <div class="items" id="cat1"></div>
                </details>
            </div>


            <div class="accordion">
                <details>
                    <summary>Zabezpieczenia i kolizje</summary>
                    <div class="items" id="cat2"></div>
                </details>
            </div>


            
            <div class="accordion">
                <details>
                    <summary>Holowanie / Transport</summary>
                    <div class="items" id="cat3"></div>
                </details>
            </div>

            <div class="accordion">
                <details>
                    <summary>Dodatki</summary>
                    <div class="items" id="cat4"></div>
                </details>
            </div>

            <div class="total">
                <span>Łączna kwota:</span>
                <span id="totalPrice">0 PLN</span>
            </div>
        </div>

        <button class="sendBtn" onclick="sendInvoice()">Wyślij na Discord</button>
            <button class="clearBtn" onclick="clearAll()">Wyczyść</button>
        </div>

        <div class="preview" id="preview">
        <div class="previewContent">
            <h3>Podgląd faktury</h3>
            <div class="line"></div>
            <p><b>Numer faktury:</b> <span id="invoiceNumber">FAK-0001</span></p>
            <p><b>Wystawiający:</b> <span id="previewIssuer">Brak</span></p>
            <p><b>Klient:</b> <span id="previewClient">Brak</span></p>
            <p><b>Zakres świadczenia:</b> <span id="previewScope">Brak</span></p>
            <p><b>Miejsce:</b> <span id="previewPlace">Brak</span></p>
            <p><b>Data i godzina:</b> <span id="previewDate">Brak</span></p>
            <p><b>Pojazd:</b> <span id="previewCar">Brak (Brak)</span></p>
            <div class="line"></div>
            <p><b>Usługi:</b></p>
            <p class="servicesList" id="previewServices"></p>
            <div class="line"></div>
            <p><b>Razem:</b> <span id="previewTotal">0 PLN</span></p>
            <p><b>Termin spłaty:</b> 7 dni</p>
            <p><b>Opłaty należy kierować do</b> Dawid Jagieloński lub Amebaa</p>
            <p><b>Brak spłaty oznacza wszczęcie postępowania</b></p>
        </div>
    </div>
</div>

<script>
    // Konta użytkowników
    const users = {
        'GDDKIA': { password: 'Jagieloński', role: 'GDD-01 Dawid Jagieloński (Zarząd)' },
        'Dawid Adamski': { password: '1144', role: 'GDD-02 Dawid Adamski (Zarząd)' },
        '1234567890': { password: '1234567890', role: 'GDD-42 Maciej Zapała' },
        'TomekDun1998': { password: 'Tomduncz1998', role: 'GDD- Tomasz Duńczyk' },
        'marcel123': { password: 'marcel123', role: 'GDD-43 Dawid Bieg' },
    
        
    };

    function checkLogin() {
        const username = document.getElementById("usernameInput").value;
        const password = document.getElementById("passwordInput").value;

        if(users[username] && users[username].password === password) {
            const role = users[username].role;

            document.getElementById("passwordModal").style.display = "none";
            document.getElementById("mainContent").style.display = "block";
            document.getElementById("roleDisplay").innerText = `Zalogowany jako: ${role}`;
            document.getElementById("wrongPassword").style.display = "none";

            // Sekcje po roli (tu możesz dodać ograniczenia dla pracownika, admina itd.)
        } else {
            document.getElementById("wrongPassword").style.display = "block";
        }
    }

    function logout() {
        document.getElementById("passwordModal").style.display = "flex";
        document.getElementById("mainContent").style.display = "none";
        document.getElementById("usernameInput").value = '';
        document.getElementById("passwordInput").value = '';
    }

    // --- Formularz faktury ---
    let invoiceCounter = 1;

    function clearAll() {
        document.querySelectorAll("input, textarea").forEach(el => el.value = '');
        updateAll();
    }
</script>
<script>

    const categories = {
        cat1: [
            {name: "Tymczasowa naprawa", price: 1500},
            {name: "Uzupełnienie płynów eksploatacyjnych", price: 150},
            {name: "Wymiana bezpieczników / drobne naprawy elektryczne", price: 600},
            {name: "Wymiana opony [Od sztuki]", price: 400},
            {name: "Wymiana oleju", price: 300},
        ],
        cat2: [
            {name: "Zabezpieczenie pojazdu po kolizji", price: 1200},
            {name: "Zabezpieczenie miejsca kolizji", price: 2500},
            {name: "Neutralizacja wycieku oleju / paliwa", price: 1000},
            {name: "Sprzątnięcie części samochodu z drogi", price: 500},
            {name: "Odwrócenie samochodu z dachu", price: 2750},
            {name: "Usunięcie rozsypanego ładunku (do 10 min pracy)", price: 1000},
            {name: "Usunięcie rozsypanego ładunku (powyżej 10 min)", price: 2000},
            {name: "Zabezpieczenie uszkodzonej nawierzchni (tymczasowe)", price: 1700},
            {name: "Montaż tymczasowego oznakowania objazdu", price: 1000},
            {name: "Demontaż tymczasowego oznakowania", price: 500},
        ],
        cat3: [
            {name: "Wyciąganie pojazdu z rowu, błota, śniegu, piasku", price: 3500},
            {name: "Transport motocykli / quadów / pojazdów specjalnych", price: 2900},
            {name: "Odholowanie samochodu niewłaściwie zaparkowanego", price: 3000},
            {name: "Odholowanie samochodu utrudniającego wszelki przejazd", price: 5000},
            {name: "Odholowanie samochodu do mechanika", price: 1500},
            {name: "Odholowanie samochodu na parking GDDKIA", price: 3500},
            {name: "Odholowanie samochodu na parking policyjny", price: 4000},
            {name: "Odholowanie pojazdu z pasa awaryjnego autostrady", price: 3500},
            {name: "Odholowanie pojazdu blokującego bramki / zjazd", price: 4000},
            {name: "Odholowanie pojazdu porzuconego", price: 3000},
        ],
        cat4: [
            {name: "Blokada jednego pasa ruchu", price: 1000},
            {name: "Blokada więcej niż jednego pasa ruchu", price: 3000},
            {name: "Użycie generatora", price: 300},
            {name: "Użycie znaków drogowych [np. stop]", price: 100},
            {name: "Użycie bariery [Za sztuke]", price: 300},
            {name: "Użycie pachołków [Za sztuke]", price: 50},
            {name: "Obsługa kolizji wielopojazdowej", price: 2000},
            {name: "Obsługa wypadku z osobami poszkodowanymi", price: 5000},
            {name: "Długotrwałe zamknięcie drogi (powyżej 30 min)", price: 2500},
            {name: "Kierowanie ruchem przez GDDKiA", price: 1500},
            {name: "Pełne zabezpieczenie miejsca wypadku (noc / ograniczona widoczność)", price: 2500},
            {name: "Postój techniczny bez uzasadnienia (interwencja)", price: 800},
            {name: "Praca w godzinach nocnych (22:00–6:00)", price: 500},
            {name: "Bezpodstawne / fałszywe zgłoszenie", price: 1000},
            {name: "Interwencja podczas burzy / śnieżycy", price: 1500},
        ]
    };

    for (let cat in categories) {
        const container = document.getElementById(cat);
        categories[cat].forEach((s, idx) => {
            const div = document.createElement("div");
            div.className = "item";
            div.innerHTML = `
                <input type="checkbox" id="${cat}-${idx}" data-price="${s.price}">
                <div>
                    <div class="name">${s.name}</div>
                    <div class="price">${s.price} PLN</div>
                </div>
            `;
            container.appendChild(div);
            div.querySelector("input").addEventListener("change", updateAll);
        });
    }

    function generateInvoiceNumber() {
        return "FAK-" + String(invoiceCounter).padStart(4, '0');
    }

    function updateAll() {
        document.getElementById("previewIssuer").innerText =
            document.getElementById("issuerNick").value || "Brak";
        document.getElementById("previewClient").innerText =
            document.getElementById("clientName").value || "Brak";
        document.getElementById("previewScope").innerText =
            document.getElementById("scope").value || "Brak";
        document.getElementById("previewPlace").innerText =
            document.getElementById("place").value || "Brak";
        document.getElementById("previewDate").innerText =
            document.getElementById("dateTime").value || "Brak";
        document.getElementById("previewCar").innerText =
            `${document.getElementById("vehicleType").value || "Brak"} (${document.getElementById("carPlate").value || "Brak"})`;

        let total = 0;
        let selected = [];
        document.querySelectorAll("input[type=checkbox]").forEach(ch => {
            if (ch.checked) {
                total += parseInt(ch.dataset.price);
                selected.push(ch.parentElement.querySelector(".name").innerText + " - " + ch.dataset.price + " PLN");
            }
        });

        document.getElementById("totalPrice").innerText = total + " PLN";
        document.getElementById("previewTotal").innerText = total + " PLN";
        document.getElementById("previewServices").innerText = selected.join("\n");
    }

    function clearAll() {
        document.getElementById("issuerNick").value = "";
        document.getElementById("issuerIC").value = "";
        document.getElementById("clientName").value = "";
        document.getElementById("clientNick").value = "";
        document.getElementById("scope").value = "";
        document.getElementById("place").value = "";
        document.getElementById("dateTime").value = "";
        document.getElementById("vehicleType").value = "";
        document.getElementById("carPlate").value = "";

        document.querySelectorAll("input[type=checkbox]").forEach(ch => ch.checked = false);

        updateAll();
    }

    function sendInvoice() {
        const webhookUrl = "https://discord.com/api/webhooks/1463222849031372933/T3TfMb0pY66UQ4lP9U0Hxkq4mp-W0rIZmNqQTv6hf2szGBtw8YVPpAOf8SLnV_0-5BRo";

        const invoiceNumber = generateInvoiceNumber();
        document.getElementById("invoiceNumber").innerText = invoiceNumber;
        invoiceCounter++;

        const issuerNick = document.getElementById("issuerNick").value;
        const issuerIC = document.getElementById("issuerIC").value;
        const clientName = document.getElementById("clientName").value;
        const clientNick = document.getElementById("clientNick").value;
        const scope = document.getElementById("scope").value;
        const place = document.getElementById("place").value;
        const dateTime = document.getElementById("dateTime").value;
        const vehicleType = document.getElementById("vehicleType").value;
        const carPlate = document.getElementById("carPlate").value;

        let total = 0;
        let selected = [];
        document.querySelectorAll("input[type=checkbox]").forEach(ch => {
            if (ch.checked) {
                total += parseInt(ch.dataset.price);
                selected.push(ch.parentElement.querySelector(".name").innerText + " - " + ch.dataset.price + " PLN");
            }
        });

        const data = {
            embeds: [{
                title: `Faktura #${invoiceNumber}`,
                color: 16766720,
                fields: [
                    { name: "Wystawiający (DC)", value: issuerNick || "Brak", inline: true },
                    { name: "Wystawiający (IC)", value: issuerIC || "Brak", inline: true },
                    { name: "Klient (IC)", value: clientName || "Brak", inline: true },
                    { name: "Klient (DC)", value: clientNick || "Brak", inline: true },
                    { name: "Zakres świadczenia", value: scope || "Brak", inline: false },
                    { name: "Miejsce zdarzenia", value: place || "Brak", inline: true },
                    { name: "Data i godzina", value: dateTime || "Brak", inline: true },
                    { name: "Rodzaj pojazdu", value: `${vehicleType || "Brak"} (${carPlate || "Brak"})`, inline: false },
                    { name: "Usługi", value: selected.length ? selected.join("\n") : "Brak", inline: false },
                    { name: "Kwota do zapłaty", value: total + " PLN", inline: true },
                    { name: "Termin spłaty", value: "7 dni", inline: true },
                    { name: "Konsekwencje braku spłaty", value: "Brak spłaty oznacza wszczęcie postępowania", inline: false }
                ],
                footer: { text: "System faktur RP" }
            }]
        };

        fetch(webhookUrl, {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(data)
        }).then(res => {
            if (res.ok) {
                alert("Faktura wysłana!");
                clearAll(); // <-- automatyczne czyszczenie po wysłaniu
            } else {
                alert("Błąd wysyłki!");
            }
        });
    }

    document.querySelectorAll("input, textarea").forEach(el => {
        el.addEventListener("input", updateAll);
    });

    updateAll();
</script>

</body>
</html>
