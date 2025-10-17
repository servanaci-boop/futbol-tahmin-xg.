import streamlit as st
import requests
import pandas as pd

# API-Football anahtarınızı buraya girin
API_KEY = 'your_api_key'
BASE_URL = 'https://api-football-v1.p.rapidapi.com/v3/'

# Başlık
st.title("⚽ Canlı xG Tahmin Arayüzü")

# Takım isimlerini al
home_team = st.text_input("Ev sahibi takım adı:")
away_team = st.text_input("Deplasman takım adı:")

# API'den xG verilerini al
def get_xg_data(team):
    url = f"{BASE_URL}teams/statistics"
    params = {
        'league': '39',  # Premier League için örnek
        'team': team
    }
    headers = {
        'X-RapidAPI-Key': API_KEY,
        'X-RapidAPI-Host': 'api-football-v1.p.rapidapi.com'
    }
    response = requests.get(url, headers=headers, params=params)
    data = response.json()
    return data['response'][0]['statistics']

# Tahmin fonksiyonu
def predict_match(home, away):
    home_stats = get_xg_data(home)
    away_stats = get_xg_data(away)
    
    # xG farkını hesapla
    xg_diff = home_stats['goals']['expected'] - away_stats['goals']['expected']
    
    # Üst/Alt 2.5 tahmini
    total_goals = home_stats['goals']['expected'] + away_stats['goals']['expected']
    over_under = "Üst 2.5" if total_goals > 2.5 else "Alt 2.5"
    
    # BTTS tahmini
    btts = "Evet" if home_stats['goals']['expected'] > 1 and away_stats['goals']['expected'] > 1 else "Hayır"
    
    # Beklenen skor
    home_score = round(home_stats['goals']['expected'], 1)
    away_score = round(away_stats['goals']['expected'], 1)
    
    # Sonuç
    result = {
        "Ev Takımı": home,
        "Dep Takımı": away,
        "Ev xG": home_stats['goals']['expected'],
        "Dep xG": away_stats['goals']['expected'],
        "Üst/Alt 2.5": over_under,
        "BTTS": btts,
        "Beklenen Skor": f"{home_score}-{away_score}",
        "xG Farkı": round(xg_diff, 2)
    }
    
    return result

# Tahmin butonu
if st.button("Tahmin Al"):
    if home_team and away_team:
        prediction = predict_match(home_team, away_team)
        df = pd.DataFrame([prediction])
        st.dataframe(df)
        
        # CSV olarak indir
        csv = df.to_csv(index=False).encode('utf-8')
        st.download_button("CSV İndir", csv, "match_prediction.csv", "text/csv")
    else:
        st.error("Lütfen her iki takımın adını da girin.")
