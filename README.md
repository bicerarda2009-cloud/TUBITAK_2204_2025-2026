# TUBITAK_2204_2025-2026
# Ders çizelgeleme problemi

import random

# ---------------------------------------------------------------------
# Ders verileri
# ---------------------------------------------------------------------
dersler = {
    "Edebiyat":   {"tip": "sözel",   "adet": 4},
    "Matematik":  {"tip": "sayısal", "adet": 6},
    "Kimya":      {"tip": "sayısal", "adet": 2},
    "Fizik":      {"tip": "sayısal", "adet": 2},
    "Biyoloji":   {"tip": "sayısal", "adet": 2},
    "Tarih":      {"tip": "sözel",   "adet": 2},
    "Coğrafya":   {"tip": "sözel",   "adet": 2},
    "Felsefe":    {"tip": "sözel",   "adet": 2},
    "İngilizce":  {"tip": "sözel",   "adet": 4},
    "Almanca":    {"tip": "sözel",   "adet": 2},
    "Din":        {"tip": "sözel",   "adet": 2},
}

dil_dersleri = ["İngilizce", "Almanca", "Edebiyat"]
MATEMATIK = "Matematik"
ağır_dersler = ["Matematik", "Edebiyat"]


# ---------------------------------------------------------------------
# Blok oluşturma
# ---------------------------------------------------------------------
def blok_ders_listesi():
    bloklar = []
    for ders, info in dersler.items():
        adet = info["adet"] // 2
        for _ in range(adet):
            bloklar.append((ders, info["tip"]))
    return bloklar


# ---------------------------------------------------------------------
# Kısıt 1 — Sözel ≤ 2 × sayısal
# ---------------------------------------------------------------------
def oran_uygun_mu(program, yeni):
    soz = sum(1 for _, t in program + [yeni] if t == "sözel")
    say = sum(1 for _, t in program + [yeni] if t == "sayısal")
    if say == 0:
        return True
    return soz <= 2 * say


# ---------------------------------------------------------------------
# Kısıt 2 — Aynı ders ardışık olmasın
# Kısıt 3 — Dil dersleri ardışık olmasın
# Kısıt 4 — Ağır dersler arkaya arkaya gelmesin
# ---------------------------------------------------------------------
def ardarda_kisit(program, yeni):
    if not program:
        return True

    yeni_ders = yeni[0]
    son_ders = program[-1][0]

    # 1) Aynı ders ardışık olmasın
    if yeni_ders == son_ders:
        return False

    # 2) Aynı gün içinde yalnızca 1 dil dersi olsun
    if yeni_ders in dil_dersleri:
        if any(ders in dil_dersleri for ders, _ in program):
            return False
    # 3) Ağır dersler arka arkaya gelmesin
    if yeni_ders in ağır_dersler and son_ders in ağır_dersler:
        return False

    return True


# ---------------------------------------------------------------------
# Kısıt 5 — Her gün en az 1 sayısal ders olsun
# ---------------------------------------------------------------------
def gun_sayisal_kisit(haftalik):
    for gün, dersler_günü in haftalik.items():
        if len(dersler_günü) != 3:
            continue
        say = sum(1 for _, t in dersler_günü if t == "sayısal")
        if say == 0:
            return False
    return True


#----------------------------------------------------------------------
# Kısıt 6 - Her gün en fazla 1 matematik dersi olsun
#----------------------------------------------------------------------
def gunluk_matematik_kisiti(program, yeni):
    yeni_ders = yeni[0]


    if yeni_ders == MATEMATIK:
        if any(ders == MATEMATIK for ders, _ in program):
            return False

    return True


# ---------------------------------------------------------------------
# Tek bir şube için program oluşturma
# ---------------------------------------------------------------------
def program_olustur():
    günler = ["Pazartesi", "Salı", "Çarşamba", "Perşembe", "Cuma"]
    haftalık = {g: [] for g in günler}

    bloklar = blok_ders_listesi()
    random.shuffle(bloklar)

    for ders, tip in bloklar:
        yerlesti = False
        random.shuffle(günler)

        for gün in günler:
            gün_programı = haftalık[gün]

            if len(gün_programı) >= 3:
                continue

            yeni = (ders, tip)

            if not oran_uygun_mu(gün_programı, yeni):
                continue

            if not ardarda_kisit(gün_programı, yeni):
                continue

            if not gunluk_matematik_kisiti(gün_programı, yeni):
                continue

            gün_programı.append(yeni)
            yerlesti = True
            break


        if not yerlesti:
            return None

    if not gun_sayisal_kisit(haftalık):
        return None

    return haftalık


# ---------------------------------------------------------------------
# 4 Şube İçin Program Üretme
# ---------------------------------------------------------------------
subeler = ["A", "B", "C", "D"]
tum_programlar = {}

for sube in subeler:
    while True:
        p = program_olustur()
        if p is not None:
            tum_programlar[sube] = p
            break

# ---------------------------------------------------------------------
# ÇIKTI
# ---------------------------------------------------------------------
for sube, program in tum_programlar.items():
    print(f"\n\n===== {sube} ŞUBESİ DERS PROGRAMI =====")
    for gün, dersler_günü in program.items():
        print(f"\n--- {gün} ---")
        for i in range(3):
            if i < len(dersler_günü):
                print(f"{2*i+1}-{2*i+2}. saat: {dersler_günü[i][0]}")
            else:
                print(f"{2*i+1}-{2*i+2}. saat: *BOŞ*")
