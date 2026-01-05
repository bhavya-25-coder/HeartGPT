AI system that works only on heart and cardiac-related topics


CODE:
import streamlit as st
from openai import OpenAI

# 🔑 OpenAI Client
client = OpenAI(
    api_key="add ur API"
)

# -----------------------------
# PAGE CONFIG
# -----------------------------
st.set_page_config(
    page_title="HeartGPT – Cardiology Assistant",
    page_icon="🫀",
    layout="centered"
)

# -----------------------------
# BLACK HOSPITAL BACKGROUND + HIGH CONTRAST TEXT
# -----------------------------
st.markdown(
    """
    <style>
    .stApp {
        background-color: #000000;
        color: #e6f1ff;
    }

    .block-container {
        background-color: rgba(15, 25, 40, 0.95);
        padding: 2.5rem;
        border-radius: 14px;
        box-shadow: 0px 0px 20px rgba(0, 255, 200, 0.15);
    }

    h1 {
        color: #00e5ff;
        text-align: center;
        font-weight: 800;
    }

    h3 {
        color: #7fdcff;
        text-align: center;
        margin-top: -10px;
    }

    p, label, span, div {
        color: #e6f1ff !important;
        font-size: 16px;
    }

    .stChatMessage {
        background-color: transparent;
    }
    </style>
    """,
    unsafe_allow_html=True
)

# -----------------------------
# IMPRESSIVE HEADING
# -----------------------------
st.markdown(
    """
    <h1>🫀 HeartGPT 🩺</h1>
    <h3>Real-Time Cardiology Conversational AI System</h3>
    """,
    unsafe_allow_html=True
)

st.markdown(
    "🟦 **A real-time AI assistant strictly limited to heart and cardiac-related topics.**"
)

# -----------------------------
# HEART DOMAIN FILTER
# -----------------------------
HEART_KEYWORDS = [
    "heart", "cardiac", "bp", "blood pressure", "hypertension",
    "cholesterol", "ecg", "ekg", "heart attack", "myocardial",
    "artery", "vein", "pulse", "stent", "angioplasty",
    "arrhythmia", "heart rate", "coronary", "valve"
]

def is_heart_related(text):
    return any(word in text.lower() for word in HEART_KEYWORDS)

# -----------------------------
# SESSION STATE
# -----------------------------
if "messages" not in st.session_state:
    st.session_state.messages = []

# -----------------------------
# DISPLAY CHAT HISTORY
# -----------------------------
for msg in st.session_state.messages:
    if msg["role"] == "user":
        with st.chat_message("user", avatar="👤"):
            st.markdown(msg["content"])
    else:
        with st.chat_message("assistant", avatar="🩺"):
            st.markdown(msg["content"])

# -----------------------------
# USER INPUT
# -----------------------------
user_input = st.chat_input("Enter a heart-related question")

if user_input:
    st.session_state.messages.append(
        {"role": "user", "content": user_input}
    )

    with st.chat_message("user", avatar="👤"):
        st.markdown(user_input)

    # ❌ Non-heart questions
    if not is_heart_related(user_input):
        refusal = (
            "❌ **I can only answer questions related to the heart and cardiac health.**"
        )

        st.session_state.messages.append(
            {"role": "assistant", "content": refusal}
        )

        with st.chat_message("assistant", avatar="🩺"):
            st.markdown(refusal)

    # ✅ Heart-related questions
    else:
        with st.spinner("🫀 HeartGPT is analyzing cardiac data..."):
            response = client.chat.completions.create(
                model="gpt-4o-mini",
                messages=[
                    {
                        "role": "system",
                        "content": (
                            "You are HeartGPT, a professional cardiology AI assistant. "
                            "Answer ONLY heart and cardiac-related questions. "
                            "If a question is outside cardiology, reply exactly: "
                            "'I can only answer heart and cardiac-related questions.' "
                            "Do not diagnose or provide emergency treatment. "
                            "Use professional, educational medical language."
                        )
                    }
                ] + st.session_state.messages
            )

            answer = response.choices[0].message.content

        st.session_state.messages.append(
            {"role": "assistant", "content": answer}
        )

        with st.chat_message("assistant", avatar="🩺"):
            st.markdown(answer)

