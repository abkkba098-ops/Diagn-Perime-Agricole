import streamlit as st
import pandas as pd
import sqlite3
import os

# Configuration de la page
st.set_page_config(page_title="Diagnostics Agricoles Djibouti", layout="wide")

st.title("🌾 Gestion des Périmètres Agricoles")
st.sidebar.header("Configuration")

# Connexion Base de Données
conn = sqlite3.connect('agri_djibouti.db')

# ZONE D'IMPORTATION (Pour toi ou ton boss)
with st.sidebar.expander("Importer des données Excel"):
    uploaded_file = st.file_uploader("Choisir un fichier Excel", type=['xlsx'])
    if uploaded_file:
        df = pd.read_excel(uploaded_file)
        table_name = st.text_input("Nom de la table (ex: parcelles)", "nouvelle_table")
        if st.button("Charger dans la base"):
            df.to_sql(table_name, conn, if_exists='replace', index=False)
            st.success(f"Table '{table_name}' mise à jour !")

# ZONE DE CONSULTATION (L'interface pour les utilisateurs)
st.subheader("🔍 Consultation de la base de données")

# Récupérer la liste des tables existantes
try:
    tables = pd.read_sql("SELECT name FROM sqlite_master WHERE type='table'", conn)
    if not tables.empty:
        option = st.selectbox("Choisissez la catégorie à consulter :", tables['name'].tolist())
        
        # Barre de recherche
        search = st.text_input(f"Rechercher dans {option}...")
        
        # Affichage des données
        data = pd.read_sql(f"SELECT * FROM {option}", conn)
        
        if search:
            # Filtre simple qui cherche dans toutes les colonnes
            data = data[data.astype(str).apply(lambda x: x.str.contains(search, case=False)).any(axis=1)]
        
        st.dataframe(data, use_container_width=True)
        
        # Bouton d'export
        st.download_button("Télécharger ce tableau en Excel", 
                           data.to_csv().encode('utf-8'), 
                           "export_data.csv", "text/csv")
    else:
        st.info("La base de données est vide. Importez un fichier Excel dans le menu à gauche.")
except Exception as e:
    st.error("Erreur lors de la lecture des données.")
