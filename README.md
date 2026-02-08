import streamlit as st
import pandas as pd
import sqlite3

st.set_page_config(page_title="Agriculture Djibouti", layout="wide")

st.title("🌾 Base de Données Agricole - Djibouti")

# Connexion SQLite
conn = sqlite3.connect('database.db')

# Menu latéral pour l'administration
with st.sidebar:
    st.header("Admin")
    uploaded_file = st.file_uploader("Importer un Excel", type="xlsx")
    if uploaded_file:
        df = pd.read_excel(uploaded_file)
        df.to_sql("data", conn, if_exists="replace", index=False)
        st.success("Données mises à jour !")

# Affichage des données
try:
    df_view = pd.read_sql("SELECT * FROM data", conn)
    st.subheader("Consultation des Périmètres")
    
    # Barre de recherche
    search = st.text_input("Rechercher un exploitant, une région ou une parcelle...")
    if search:
        df_view = df_view[df_view.astype(str).apply(lambda x: x.str.contains(search, case=False)).any(axis=1)]
    
    st.dataframe(df_view, use_container_width=True)
except:
    st.info("Bienvenue ! Veuillez importer un fichier Excel via le menu latéral pour commencer.")
