———————————————————————————————————————————————————————————————
portfólio de Yudi Matsumoto

lista projetos :

———————————————————————————————————————————————————————————————

  projeto simples teste. (farenheit em graus celsius)
  
graus_farenheit = float(input("Digite a temperatura em Farenheit: "))
graus_celsius = 5 * (graus_farenheit - 32) / 9
print(
    f"{graus_farenheit:.2f} graus Farenheit correspondem a "
    f"{graus_celsius:.2f} graus Celsius"
)

———————————————————————————————————————————————————————————————

 projeto . FastAPI 
 
 (terminal) pip install fastapi uvicorn bcrypt python-jose

 ———————————————————————————————————————————————————————————————
 
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import bcrypt
from jose import jwt
import uuid

app = FastAPI()

SECRET = "segredo_super"

users = []

class UserCreate(BaseModel):
    nome: str
    email: str
    senha: str

class Login(BaseModel):
    email: str
    senha: str

@app.post("/users")
def criar_usuario(user: UserCreate):

    senha_hash = bcrypt.hashpw(user.senha.encode(), bcrypt.gensalt())

    novo_user = {
        "id": str(uuid.uuid4()),
        "nome": user.nome,
        "email": user.email,
        "senha": senha_hash
    }

    users.append(novo_user)

    return {"mensagem": "Usuário criado", "user": novo_user}

@app.get("/users")
def listar_users():
    return users

@app.get("/users/{user_id}")
def pegar_user(user_id: str):

    for u in users:
        if u["id"] == user_id:
            return u

    raise HTTPException(status_code=404, detail="Usuário não encontrado")

@app.delete("/users/{user_id}")
def deletar_user(user_id: str):

    global users

    users = [u for u in users if u["id"] != user_id]

    return {"mensagem": "Usuário deletado"}

@app.post("/login")
def login(login: Login):

    for u in users:

        if u["email"] == login.email:

            if bcrypt.checkpw(login.senha.encode(), u["senha"]):

                token = jwt.encode(
                    {"user_id": u["id"]},
                    SECRET,
                    algorithm="HS256"
                )

                return {"token": token}

    raise HTTPException(status_code=401, detail="Credenciais inválidas")

@app.get("/")
def home():
    return {"mensagem": "API funcionando"}
———————————————————————————————————————————————————————————————
importante:
/docs 
uvicorn main:app --reload
———————————————————————————————————————————————————————————————
