# ============================================================
# MONEY UP – VERSÃO FINAL 10/10
# Bancos Supabase • Tema Dark/Light • Logo Dinâmica • Premium
# ============================================================

import streamlit as st
import pandas as pd
import plotly.express as px
from datetime import datetime
from supabase import create_client
import hashlib

# ==================== CONFIGURAÇÃO ====================
st.set_page_config(
    page_title="Money Up",
    page_icon="🚀",
    layout="centered",
    initial_sidebar_state="expanded",
)

# ==================== TEMA DINÂMICO ====================
if "theme" not in st.session_state:
    st.session_state["theme"] = "light"

THEME = st.session_state["theme"]

if THEME == "light":
    BG_COLOR = "#FFF8C4"
    SIDEBAR_COLOR = "#A8E6CF"
    TEXT_COLOR = "#2E7D32"
    BTN_COLOR = "#2E7D32"
else:
    BG_COLOR = "#1E1E1E"
    SIDEBAR_COLOR = "#333333"
    TEXT_COLOR = "#00FFAA"
    BTN_COLOR = "#00CC88"

# CSS DINÂMICO
st.markdown(
    f"""
    <style>
    .stApp {{
        background-color: {BG_COLOR} !important;
    }}
    [data-testid="stSidebar"] {{
        background-color: {SIDEBAR_COLOR} !important;
    }}
    h1, h2, h3, h4, h5, h6, label, p, span {{
        color: {TEXT_COLOR} !important;
    }}
    .stButton>button {{
        background-color: {BTN_COLOR} !important;
        color: white !important;
        border-radius: 10px !important;
        font-weight: bold !important;
        border: none !important;
    }}
    </style>
    """,
    unsafe_allow_html=True,
)

# ==================== LOGO OFICIAL ====================
LOGO_LIGHT = """
<svg width="160" height="160" xmlns="http://www.w3.org/2000/svg">
  <rect width="160" height="160" rx="30" fill="#2E7D32"/>
  <circle cx="80" cy="80" r="55" fill="#A8E6CF"/>
  <text x="50%" y="54%" text-anchor="middle" fill="#2E7D32"
        font-size="38px" font-family="Arial" font-weight="bold">
        MU
  </text>
</svg>
"""

LOGO_DARK = """
<svg width="160" height="160" xmlns="http://www.w3.org/2000/svg">
  <rect width="160" height="160" rx="30" fill="#00CC88"/>
  <circle cx="80" cy="80" r="55" fill="#1E1E1E"/>
  <text x="50%" y="54%" text-anchor="middle" fill="#00CC88"
        font-size="38px" font-family="Arial" font-weight="bold">
        MU
  </text>
</svg>
"""

# ==================== SUPABASE ====================
@st.cache_resource
def get_supabase_client():
    return create_client(
        st.secrets["SUPABASE_URL"],
        st.secrets["SUPABASE_KEY"]
    )

supabase = get_supabase_client()

# ==================== CONSTANTES ====================
LIMITE_FREE = 25

# ==================== FUNÇÕES ====================
def hashear(texto):
    return hashlib.sha256(texto.encode()).hexdigest()


def formatar(valor):
    return f"R$ {valor:,.2f}".replace(",", "X").replace(".", ",").replace("X", ".")


def carregar_transacoes(usuario):
    resp = (
        supabase.table("transacoes")
        .select("*")
        .eq("usuario", usuario)
        .order("data")
        .execute()
    )
    df = pd.DataFrame(resp.data)
    if not df.empty:
        df["data"] = pd.to_datetime(df["data"])
    return df


def inserir_transacao(registro):
    supabase.table("transacoes").insert(registro).execute()

# ==================== LOGIN ====================
USUARIOS = {
    "antonio": hashear("123"),
    "maria": hashear("123"),
}

if "usuario" not in st.session_state:
    st.session_state["usuario"] = None

if "premium" not in st.session_state:
    st.session_state["premium"] = False

if st.session_state["usuario"] is None:
    st.markdown("<h1 style='text-align:center;'>Money Up</h1>", unsafe_allow_html=True)
    st.markdown("<p style='text-align:center;'>Faça seu login</p>", unsafe_allow_html=True)

    usuario = st.text_input("Usuário")
    senha = st.text_input("Senha", type="password")

    if st.button("Entrar"):
        if usuario in USUARIOS and hashear(senha) == USUARIOS[usuario]:
            st.session_state["usuario"] = usuario
            st.rerun()
        else:
            st.error("Usuário ou senha inválidos")

    st.stop()

# ==================== CARREGAR DADOS ====================
USER = st.session_state["usuario"]
PREMIUM = st.session_state["premium"]
df = carregar_transacoes(USER)

# ==================== SIDEBAR ====================
with st.sidebar:

    # Logo dinâmica
    st.markdown(LOGO_DARK if THEME == "dark" else LOGO_LIGHT, unsafe_allow_html=True)

    # Botão tema
    if st.button("🌙 / ☀️ Tema"):
        st.session_state["theme"] = "dark" if THEME == "light" else "light"
        st.rerun()

    st.markdown("## Money Up")
    st.write(f"👤 Usuário: **{USER}**")

    total = df["valor"].sum() if not df.empty else 0
    st.metric("Saldo Total", formatar(total))

    if PREMIUM:
        st.success("Plano: Premium ✔")
    else:
        st.info(f"Plano: Free • {len(df)}/{LIMITE_FREE} lançamentos")

    st.markdown("---")

    menu = st.radio(
        "Navegação",
        ["Dashboard", "Metas Mensais", "Planos Money Up"]
    )

    if st.button("Sair"):
        st.session_state["usuario"] = None
        st.session_state["premium"] = False
        st.rerun()

# ==================== TELA: PLANOS ====================
if menu == "Planos Money Up":
    st.title("Planos Money Up")
    st.markdown("---")

    st.subheader("Free")
    st.markdown("""
    • Até 25 lançamentos  
    • Filtros completos  
    • ❌ Sem gráficos  
    • ❌ Sem exportar CSV  
    """)

    st.subheader("Premium")
    st.markdown("""
    ✔ Lançamentos ilimitados  
    ✔ Gráficos liberados  
    ✔ Exportação CSV  
    ✔ Melhor experiência  
    """)

    st.markdown("---")

    col1, col2 = st.columns(2)
    with col1:
        if st.button("Ativar Premium Mensal"):
            st.session_state["premium"] = True
            st.success("Premium ativado!")
            st.rerun()

    with col2:
        if st.button("Ativar Premium Anual"):
            st.session_state["premium"] = True
            st.success("Premium ativado!")
            st.rerun()

    st.stop()

# ==================== TELA: METAS ====================
if menu == "Metas Mensais":
    st.title("Metas Mensais")

    if df.empty:
        st.info("Nenhum lançamento ainda.")
        st.stop()

    mes_atual_period = datetime.now().to_period("M")
    df_mes = df[df["data"].dt.to_period("M") == mes_atual_period]

    meta = st.number_input("Meta de economia no mês", min_value=0.0)

    gasto = df_mes["valor"].sum()
    economia = meta - abs(gasto)

    st.metric("Gasto no mês", formatar(abs(gasto)))
    st.metric("Falta para bater a meta", formatar(economia))

    if PREMIUM and not df_mes.empty:
        fig = px.bar(
            x=["Meta", "Gasto", "Diferença"],
            y=[meta, abs(gasto), economia]
        )
        st.plotly_chart(fig)
    else:
        st.warning("Gráficos disponíveis apenas no Premium.")

    st.stop()

# ==================== DASHBOARD ====================
st.markdown(f"<h1 style='color:{TEXT_COLOR};'>Money Up</h1>", unsafe_allow_html=True)

if PREMIUM:
    st.success("Assinante Premium ✔")
else:
    st.info(f"Plano Free: {len(df)}/{LIMITE_FREE} lançamentos")

# ==================== FORMULÁRIO ====================
st.subheader("Adicionar lançamento")

col1, col2 = st.columns(2)
with col1:
    data = st.date_input("Data", datetime.today())
    desc = st.text_input("Descrição")
    conta = st.selectbox("Conta", ["Nubank", "Carteira", "Itaú", "Caixa"])
    categoria = st.selectbox(
        "Categoria",
        ["Alimentação", "Transporte", "Moradia", "Saúde", "Lazer", "Roupas", "Educação", "Outros"]
    )

with col2:
    valor = st.number_input("Valor R$", min_value=0.01)
    tipo = st.selectbox("Tipo", ["Receita", "Despesa", "Cartão de Crédito"])
    parcelas = st.slider("Parcelas", 1, 24, 1) if tipo == "Cartão de Crédito" else 1

# BLOQUEIO FREE
if not PREMIUM and len(df) >= LIMITE_FREE:
    st.error("Você atingiu o limite de 25 lançamentos do plano Free.")
else:
    if st.button("Salvar lançamento", use_container_width=True):
        for i in range(parcelas):
            nova_data = data + pd.DateOffset(months=i)
            nova_desc = f"{desc} ({i+1}/{parcelas})" if parcelas > 1 else desc
            novo_valor = valor / parcelas if parcelas > 1 else valor

            registro = {
                "usuario": USER,
                "data": str(nova_data.date()),
                "descricao": nova_desc,
                "valor": float(novo_valor if tipo == "Receita" else -novo_valor),
                "tipo": tipo,
                "conta": conta,
                "categoria": categoria,
            }
            inserir_transacao(registro)

        st.success("Lançamento salvo!")
        st.rerun()

df = carregar_transacoes(USER)

# ==================== GRÁFICOS ====================
st.markdown("---")
st.subheader("Gráficos")

if not PREMIUM:
    st.warning("Somente para Premium.")
else:
    if not df.empty:
        colg1, colg2 = st.columns(2)

        with colg1:
            sub = df[df["valor"] < 0]
            if not sub.empty:
                sub["abs"] = sub["valor"].abs()
                fig = px.pie(sub, names="categoria", values="abs")
                st.plotly_chart(fig)

        with colg2:
            proj = []
            agora = datetime.now()

            for i in range(12):
                per = (agora + pd.DateOffset(months=i)).to_period("M")
                s = df[df["data"].dt.to_period("M") == per]["valor"].sum()
                proj.append({"mes": per.strftime("%b/%Y"), "saldo": s})

            fig2 = px.bar(pd.DataFrame(proj), x="mes", y="saldo")
            st.plotly_chart(fig2)

# ==================== TABELA + FILTRO ====================
st.markdown("---")
st.subheader("Últimos lançamentos")

if df.empty:
    st.info("Nenhum lançamento ainda.")
    st.stop()

df_f = df.copy()

with st.expander("Filtros", expanded=True):
    colf1, colf2 = st.columns(2)
    with colf1:
        ini = st.date_input("Data inicial", df["data"].min().date())
    with colf2:
        fim = st.date_input("Data final", df["data"].max().date())

    contas = st.multiselect("Conta", sorted(df["conta"].unique()))
    tipos = st.multiselect("Tipo", sorted(df["tipo"].unique()))
    categorias = st.multiselect("Categoria", sorted(df["categoria"].unique()))

df_f = df_f[(df_f["data"] >= pd.to_datetime(ini)) & (df_f["data"] <= pd.to_datetime(fim))]

if contas:
    df_f = df_f[df_f["conta"].isin(contas)]
if tipos:
    df_f = df_f[df_f["tipo"].isin(tipos)]
if categorias:
    df_f = df_f[df_f["categoria"].isin(categorias)]

df_f2 = df_f.copy()
df_f2["data"] = df_f2["data"].dt.strftime("%d/%m/%Y")
df_f2["valor"] = df_f2["valor"].apply(lambda x: formatar(abs(x)))

st.dataframe(
    df_f2[["data", "descricao", "categoria", "valor", "tipo", "conta"]],
    use_container_width=True
)

# EXPORTAÇÃO PREMIUM
st.markdown("---")

if PREMIUM:
    st.download_button(
        "⬇️ Exportar CSV (Premium)",
        df_f2.to_csv(index=False).encode("utf-8"),
        "moneyup_export.csv",
        "text/csv",
        use_container_width=True
    )
else:
    st.warning("Exportação disponível somente no Premium.")
