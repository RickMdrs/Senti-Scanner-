import tkinter as tk
from PIL import Image, ImageTk, ImageDraw
import os
import string  # Adicionado para tratar pontuação

import nltk
from nltk.stem import RSLPStemmer

try:
    nltk.data.find('stemmers/rslp')
except LookupError:
    nltk.download('rslp')

stemmer = RSLPStemmer()

BASE_DIR = os.path.dirname(__file__)
ICON_PNG = os.path.join(BASE_DIR, "icon.png")    
BACKGROUND_PNG = os.path.join(BASE_DIR, "background.png")

# --- INÍCIO RGB BORDER ---
def criar_bordas_arredondadas(largura, altura, raio, cor_fundo):
    img = Image.new("RGBA", (largura, altura), (0, 0, 0, 0))
    draw = ImageDraw.Draw(img)
    draw.rounded_rectangle((0, 0, largura, altura), radius=raio, fill=cor_fundo)
    return ImageTk.PhotoImage(img) #aumentar o valor, diminui a velocidade da animação



def verificar_sentimento():
    texto = entrada_texto.get("1.0", "end-1c").lower()
    
    
    positivas = [
         "ótimo", "excelente", "bom","bem","maravilhoso", "top", "sensacional", "perfeito", "legal", "incrível",
    "fantástico", "admirável", "brilhante", "super", "importante", "elegante", "feliz", "alegre", "satisfatório",
    "positivo", "agradável", "encantador", "divertido", "amável", "esperançoso", "grato", "vitorioso", "animado",
    "radiante", "acolhedor", "empolgante", "magnífico", "triunfante", "sublime", "bonito", "bonita", "fofo",
    "gentil", "amoroso", "simpático", "otimista", "resiliente", "eficiente", "eficaz", "talentoso", "inteligente",
    "genial", "inspirador", "prestativo", "solidário", "fiel", "leal", "digno", "honesto", "honrado", "felicíssimo",
    "deslumbrante", "espetacular", "topíssimo", "fantabuloso", "brilhantemente", "apaixonante", "fantasia", "doce",
    "carinhoso", "abençoado", "abençoada", "próspero", "abençoado", "acolhedora", "firme", "confiante", "motivador",
    "apaixonado", "extraordinário", "fenomenal", "genial", "único", "risonho", "maduro", "crescido", "educado",
    "decente", "cordial", "radiante", "encantado", "iluminado", "divino", "respeitável", "amado", "apaixonada",
    "alegremente", "intenso", "querido", "adorável", "orgulhoso", "valente", "valoroso", "corajoso", "calmo",
    "pacífico", "justo", "campeão", "vencedor", "experiente", "habilidoso", "proativo", "criativo", "focado",
    "determinado", "vibrante", "abundante", "amável", "leve", "leveza", "brisa", "sereno", "tranquilo", "zen",
    "autêntico", "singelo", "esperançoso", "luminoso", "solene", "memóravel", "diferenciado", "fora de série",
    "formidável", "gostoso", "delicioso", "emocionante", "construtivo", "proveitoso", "relevante", "marcante",
    "enriquecedor", "engraçado", "hilário", "estiloso", "maneiro", "massa", "daora", "show", "showzaço", "nota dez",
    "dez de dez", "mil grau", "sensível", "forte", "autossuficiente", "racional", "consciente", "honrado", "aplaudido",
    "merecedor", "esperto", "justificado", "legítimo", "formoso", "abençoada", "abençoado", "sortudo", "abençoado",
    "comprometido", "concentrado", "zeloso", "protegido", "destemido", "abençoada", "valente", "ousado", "persistente",
    "gracioso", "divertidamente", "charmoso", "gentileza", "carinho", "bondoso", "campeã", "intensa", "radiante",
    "leal", "sincero", "verdadeiro", "excelentemente", "surpreendente", "reluzente", "estável", "protegida", "guerreiro",
    "felicidade", "prosperidade", "bravura", "bravamente", "alegria", "entusiasmo", "afeto", "união", "companheirismo",
    "evoluído", "madura", "alto astral", "coração quente", "cabeça boa", "bom coração", "mente limpa", "paz interior",
    "autoestima", "empatia", "respeito", "solidariedade", "compreensão", "disciplina", "ética", "humildade", "gg","otimo", "excelente", "bom", "maravilhoso", "top", "sensacional", "perfeito", "legal", "incrivel",
"fantastico", "admiravel", "brilhante", "super", "importante", "elegante", "feliz", "alegre", "satisfatorio",
"positivo", "agradavel", "encantador", "divertido", "amavel", "esperancoso", "grato", "vitorioso", "animado",
"radiante", "acolhedor", "empolgante", "magnifico", "triunfante", "sublime", "bonito", "bonita", "fofo",
"gentil", "amoroso", "simpatico", "otimista", "resiliente", "eficiente", "eficaz", "talentoso", "inteligente",
"genial", "inspirador", "prestativo", "solidario", "fiel", "leal", "digno", "honesto", "honrado", "felicissimo","ajuda"
"deslumbrante", "espetacular", "topissimo", "fantabuloso", "brilhantemente", "apaixonante", "fantasia", "doce",
"carinhoso", "abencoado", "abencoada", "prospero", "abencoado", "acolhedora", "firme", "confiante", "motivador",
"apaixonado", "extraordinario", "fenomenal", "genial", "unico", "risonho", "maduro", "crescido", "educado",
"decente", "cordial", "radiante", "encantado", "iluminado", "divino", "respeitavel", "amado", "apaixonada",
"alegremente", "intenso", "querido", "adoravel", "orgulhoso", "valente", "valoroso", "corajoso", "calmo",
"pacifico", "justo", "campeao", "vencedor", "experiente", "habilidoso", "proativo", "criativo", "focado",
"determinado", "vibrante", "abundante", "amavel", "leve", "leveza", "brisa", "sereno", "tranquilo", "zen",
"autentico", "singelo", "esperancoso", "luminoso", "solene", "memoravel", "diferenciado", "fora de serie",
"formidavel", "gostoso", "delicioso", "emocionante", "construtivo", "proveitoso", "relevante", "marcante",
"enriquecedor", "engracado", "hilario", "estiloso", "maneiro", "massa", "daora", "show", "showzaco", "nota dez",
"dez de dez", "mil grau", "sensivel", "forte", "autossuficiente", "racional", "consciente", "honrado", "aplaudido",
"merecedor", "esperto", "justificado", "legitimo", "formoso", "abencoada", "abencoado", "sortudo", "abencoado",
"comprometido", "concentrado", "zeloso", "protegido", "destemido", "abencoada", "valente", "ousado", "persistente",
"gracioso", "divertidamente", "charmoso", "gentileza", "carinho", "bondoso", "campea", "intensa", "radiante",
"leal", "sincero", "verdadeiro", "excelentemente", "surpreendente", "reluzente", "estavel", "protegida", "guerreiro",
"felicidade", "prosperidade", "bravura", "bravamente", "alegria", "entusiasmo", "afeto", "uniao", "companheirismo",
"evoluido", "madura", "alto astral", "coracao quente", "cabeca boa", "bom coracao", "mente limpa", "paz interior",
"autoestima", "empatia", "respeito", "solidariedade", "compreensao", "disciplina", "etica", "humildade", "gg"
    ]
    negativas = [
        "ruim", "péssimo", "horrível", "terrível", "lixo", "fraco", "decepcionante", "triste", "merda", "vagabundo",
    "insatisfatório", "desastroso", "frustrante", "errado", "chato", "mau", "negativo", "inadequado", "desagradável",
    "irritante", "aborrecido", "desesperado", "desanimado", "infeliz", "lamentável", "desolador", "sombrio",
    "desesperador", "repugnante", "desprezível", "injusto", "desconfortável", "feio", "feia", "mal", "nojento",
    "asqueroso", "estressante", "insuportável", "frustrado", "incompetente", "ridículo", "idiota", "burro", "lerdo",
    "medíocre", "revoltante", "raivoso", "falso", "covarde", "trapaceiro", "desleal", "hipócrita", "irresponsável",
    "preguiçoso", "pior", "cruel", "safado", "vil", "odioso", "detestável", "enjoado", "detestado", "falho", "insucesso",
    "chateado", "esquecido", "humilhado", "vergonhoso", "deprimente", "reprimido", "reprimida", "sufocante", "pesado",
    "opressor", "explorador", "abusivo", "machucado", "machucada", "traído", "traída", "sofrido", "cansado", "exausto",
    "doído", "magoado", "inseguro", "inútil", "injustiçado", "maltratado", "ferido", "zangado", "nervoso", "rejeitado",
    "indesejado", "inaceitável", "problemático", "confuso", "confusão", "raiva", "ódio", "inveja", "ciúme", "amargurado",
    "vingativo", "perturbado", "desequilibrado", "gritante", "muito ruim", "tenebroso", "catastrófico", "horrendo",
    "grotesco", "tóxico", "sórdido", "sarcástico", "provocador", "desonesto", "enganador", "destruído", "derrotado",
    "acidentado", "doente", "doentio", "mortal", "sombrio", "trágico", "horripilante", "macabro", "odiento", "decadente",
    "colapsado", "rebaixado", "oprimido", "infeliz", "insatisfeito", "amargura", "raivosamente", "tristeza", "agonia",
    "fracasso", "descontrole", "desânimo", "vazio", "torpe", "abominável", "repulsivo", "sujo", "podre", "venenoso",
    "nojenta", "inóspito", "obscuro", "frieza", "desonra", "vergonha", "culpa", "desprezo", "humilhação", "desgraçado",
    "pânico", "apavorado", "desorientado", "confundido", "retraído", "desligado", "hostil", "tenso", "cansativa",
    "malvada", "malvado", "doído", "desiludido", "desesperança", "abandono", "fracassado", "abusada", "perigoso",
    "ameaçador", "deturpado", "criminoso", "mentiroso", "escroto", "nojento", "imoral", "corrupto", "ignóbil", 
    "deturpado", "estrupado","ruim", "pessimo", "horrivel", "terrivel", "lixo", "fraco", "decepcionante", "triste", "merda", "vagabundo",
"insatisfatorio", "desastroso", "frustrante", "errado", "chato", "mau", "negativo", "inadequado", "desagradavel",
"irritante", "aborrecido", "desesperado", "desanimado", "infeliz", "lamentavel", "desolador", "sombrio",
"desesperador", "repugnante", "desprezivel", "injusto", "desconfortavel", "feio", "feia", "mal", "nojento",
"asqueroso", "estressante", "insuportavel", "frustrado", "incompetente", "ridiculo", "idiota", "burro", "lerdo",
"mediocre", "revoltante", "raivoso", "falso", "covarde", "trapaceiro", "desleal", "hipocrita", "irresponsavel",
"preguicoso", "pior", "cruel", "safado", "vil", "odioso", "detestavel", "enjoado", "detestado", "falho", "insucesso",
"chateado", "esquecido", "humilhado", "vergonhoso", "deprimente", "reprimido", "reprimida", "sufocante", "pesado",
"opressor", "explorador", "abusivo", "machucado", "machucada", "traido", "traida", "sofrido", "cansado", "exausto",
"doido", "magoado", "inseguro", "inutil", "injusticado", "maltratado", "ferido", "zangado", "nervoso", "rejeitado",
"indesejado", "inaceitavel", "problematico", "confuso", "confusao", "raiva", "odio", "inveja", "ciume", "amargurado",
"vingativo", "perturbado", "desequilibrado", "gritante", "muito ruim", "tenebroso", "catastrofico", "horrendo",
"grotesco", "toxico", "sordido", "sarcastico", "provocador", "desonesto", "enganador", "destruido", "derrotado",
"acidentado", "doente", "doentio", "mortal", "sombrio", "tragico", "horripilante", "macabro", "odiento", "decadente",
"colapsado", "rebaixado", "oprimido", "infeliz", "insatisfeito", "amargura", "raivosamente", "tristeza", "agonia",
"fracasso", "descontrole", "desanimo", "vazio", "torpe", "abominavel", "repulsivo", "sujo", "podre", "venenoso",
"nojenta", "inospito", "obscuro", "frieza", "desonra", "vergonha", "culpa", "desprezo", "humilhacao", "desgracado",
"panico", "apavorado", "desorientado", "confundido", "retraido", "desligado", "hostil", "tenso", "cansativa",
"malvada", "malvado", "doido", "desiludido", "desesperanca", "abandono", "fracassado", "abusada", "perigoso",
"ameacador", "deturpado", "criminoso", "mentiroso", "escroto", "nojento", "imoral", "corrupto", "ignobil", "deturpado", "estrupado"
    ]
    
    palavras_negacao = [
         "não", "nada", "jamais", "nunca", "nem", "nenhum", "ninguém", "de forma alguma", "de jeito nenhum",
    "de maneira alguma", "de modo algum", "de maneira nenhuma", "de modo nenhum", "em hipótese alguma",
    "nem pensar", "nem ferrando", "tá doido", "sem chance", "sem nenhuma", "nem a pau", "zero chance",
    "nenhuma hipótese", "nem morto", "nem lascando", "nem fudendo", "nunca mesmo", "nem em sonho",
    "não mesmo", "não rola", "não vai dar", "não tem como", "de jeito maneira", "de forma nenhuma",
    "de forma alguma", "não acredito", "não gostei", "nem fudendo", "impossível", "não quero", "não curti",
    "não gostei", "nem vem", "nem tente", "jamais aceitarei", "não aceito", "não concordo", "nem a pau juvenal",
    "nunca vi", "não vi", "não faço", "nunca faria", "não posso", "não devo", "proibido", "vetado", "barrado",
    "recusado", "negado", "rejeitado", "sem condição", "sem possibilidade", "nem sonhando", "nem em sonho",
    "zero chance", "desautorizado", "sem permissão", "negativamente", "jamais permitiria", "sem aval",
    "não aceitaria", "desqualificado", "inaceitável", "proibidamente", "reprovação", "sem acordo",
    "não autorizado", "sem apoio", "sem fé", "sem espírito", "sem vontade", "sem ânimo", "nem sob tortura","nao", "nada", "jamais", "nunca", "nem", "nenhum", "ninguem", "de forma alguma", "de jeito nenhum",
"de maneira alguma", "de modo algum", "de maneira nenhuma", "de modo nenhum", "em hipotese alguma",
"nem pensar", "nem ferrando", "ta doido", "sem chance", "sem nenhuma", "nem a pau", "zero chance",
"nenhuma hipotese", "nem morto", "nem lascando", "nem fudendo", "nunca mesmo", "nem em sonho",
"nao mesmo", "nao rola", "nao vai dar", "nao tem como", "de jeito maneira", "de forma nenhuma",
"de forma alguma", "nao acredito", "nao gostei", "nem fudendo", "impossivel", "nao quero", "nao curti",
"nao gostei", "nem vem", "nem tente", "jamais aceitarei", "nao aceito", "nao concordo", "nem a pau juvenal",
"nunca vi", "nao vi", "nao faco", "nunca faria", "nao posso", "nao devo", "proibido", "vetado", "barrado",
"recusado", "negado", "rejeitado", "sem condicao", "sem possibilidade", "nem sonhando", "nem em sonho",
"zero chance", "desautorizado", "sem permissao", "negativamente", "jamais permitiria", "sem aval",
"nao aceitaria", "desqualificado", "inaceitavel", "proibidamente", "reprovacao", "sem acordo",
"nao autorizado", "sem apoio", "sem fe", "sem espirito", "sem vontade", "sem animo", "nem sob tortura"
    ]
    
    intensificadores = [ "muito", "extremamente", "absolutamente", "totalmente", "super", "demais", "completamente", "tão", "realmente",
    "bastante", "incrivelmente", "altamente", "profundamente", "gigantescamente", "imensamente", "exageradamente",
    "ultra", "mega", "hiper", "tremendamente", "fortemente", "grandemente", "notavelmente", "consideravelmente",
    "excessivamente", "extraordinariamente", "fantasticamente", "maravilhosamente", "surpreendentemente",
    "espetacularmente", "radicalmente", "intensamente", "colossalmente", "monstruosamente", "absurdamente",
    "brutalmente", "violentamente", "descomunalmente", "gigantemente", "enormemente", "formidavelmente",
    "poderosamente", "potentemente", "avassaladoramente", "arrasadoramente", "devastadoramente", "fenomenalmente",
    "esplendidamente", "magnificamente", "gloriosamente", "astronomicamente", "universalmente", "mundialmente",
    "planetariamente", "celestialmente", "divinamente", "monumentalmente", "titânicamente", "herculeamente",
    "olímpicamente", "meteoricamente", "fulminantemente", "explosivamente", "fulgurantemente", "reluzentemente",
    "brilhantemente", "luminosamente", "resplandecentemente", "cintilantemente", "ofuscantemente",
    "deslumbrantemente", "arrebatadoramente", "apaixonadamente", "fervorosamente", "calorosamente",
    "ardentemente", "incansavelmente", "ininterruptamente", "constantemente", "permanentemente",
    "continuamente", "persistentemente", "insistentemente", "obstinadamente", "determinadamente",
    "decididamente", "resolutamente", "veementemente", "freneticamente", "alucinadamente", "loucamente",
    "malucamente", "doidamente", "insanamente", "absurdamente", "esquisitamente", "estranhamente",
    "inusitadamente", "raramente", "singularmente", "exclusivamente", "especialmente", "particularmente",
    "superlativamente", "ultrapassadamente", "transcendentalmente", "transbordantemente"]
    # Calculando o sentimento
    positivas_stem = set(stemmer.stem(p) for p in positivas)
    negativas_stem = set(stemmer.stem(p) for p in negativas)
    palavras_negacao_stem = set(stemmer.stem(p) for p in palavras_negacao)
    intensificadores_stem = set(stemmer.stem(p) for p in intensificadores)
    
    positivas_stem = set(stemmer.stem(p) for p in positivas)
    negativas_stem = set(stemmer.stem(p) for p in negativas)
    palavras_negacao_stem = set(stemmer.stem(p) for p in palavras_negacao)
    intensificadores_stem = set(stemmer.stem(p) for p in intensificadores)
    
    score = 0
    palavras = texto.split()
    i = 0
    while i < len(palavras):
        palavra = palavras[i].strip(string.punctuation)
        palavra_stem = stemmer.stem(palavra)

        # Verifica se é um intensificador + palavra positiva/negativa
        if palavra_stem in intensificadores_stem and i + 1 < len(palavras):
            proxima = palavras[i + 1].strip(string.punctuation)
            proxima_stem = stemmer.stem(proxima)
            if proxima_stem in positivas_stem:
                score += 2
                i += 2
                continue
            elif proxima_stem in negativas_stem:
                score -= 2
                i += 2
                continue

        # Verifica se é uma negação + palavra positiva/negativa
        elif palavra_stem in palavras_negacao_stem and i + 1 < len(palavras):
            proxima = palavras[i + 1].strip(string.punctuation)
            proxima_stem = stemmer.stem(proxima)
            if proxima_stem in positivas_stem:
                score -= 1
                i += 2
                continue
            elif proxima_stem in negativas_stem:
                score += 1
                i += 2
                continue

        # Palavras isoladas (não contar se já foi usada por intensificador/negação)
        elif palavra_stem in positivas_stem:
            if i == 0 or (stemmer.stem(palavras[i-1].strip(string.punctuation)) not in intensificadores_stem and stemmer.stem(palavras[i-1].strip(string.punctuation)) not in palavras_negacao_stem):
                score += 1
        elif palavra_stem in negativas_stem:
            if i == 0 or (stemmer.stem(palavras[i-1].strip(string.punctuation)) not in intensificadores_stem and stemmer.stem(palavras[i-1].strip(string.punctuation)) not in palavras_negacao_stem):
                score -= 1

        i += 1

    resultado.config(
        text="Sentimento: Positivo 😊", fg="#27ae60"
    ) if score > 0 else resultado.config(
        text="Sentimento: Negativo ☹️", fg="#e74c3c"
    )



root = tk.Tk()
root.title("Analisador de Sentimentos - Senti Scanner 🔍")
root.geometry("792x491")
root.resizable(False, False)

if os.path.exists(ICON_PNG):
    try:
        icon_img = tk.PhotoImage(file=ICON_PNG)
        root.iconphoto(False, icon_img)
    except tk.TclError:
        ico = os.path.join(BASE_DIR, "icon.ico")
        if os.path.exists(ico):
            root.iconbitmap(ico)

enable_bg = os.path.exists(BACKGROUND_PNG)
canvas = tk.Canvas(root, width=792, height=491, highlightthickness=8)
canvas.pack(fill="both", expand=True)
canvas.config(highlightbackground="#ff0000")  # Cor inicial da borda

# --- INÍCIO RGB BORDER SUAVE ---
def hex_to_rgb(hex_color):
    hex_color = hex_color.lstrip("#")
    return tuple(int(hex_color[i:i+2], 16) for i in (0, 2, 4))

def rgb_to_hex(rgb):
    return "#%02x%02x%02x" % rgb

def interpolate_color(c1, c2, t):
    return tuple(int(a + (b - a) * t) for a, b in zip(c1, c2))

cores_rgb_hex = [
    "#ff0000", "#ff7f00", "#ffff00", "#00ff00", "#00ffff", "#0000ff", "#4b0082", "#9400d3", "#ff00ff"
]
cores_rgb = [hex_to_rgb(c) for c in cores_rgb_hex]
cor_atual_idx = [0]
passos = 20
passo_atual = [0]

def animar_borda_rgb_suave():
    idx = cor_atual_idx[0]
    prox_idx = (idx + 1) % len(cores_rgb)
    t = passo_atual[0] / passos
    cor = interpolate_color(cores_rgb[idx], cores_rgb[prox_idx], t)
    canvas.config(highlightbackground=rgb_to_hex(cor))
    passo_atual[0] += 1
    if passo_atual[0] > passos:
        passo_atual[0] = 0
        cor_atual_idx[0] = prox_idx
    root.after(30, animar_borda_rgb_suave)

animar_borda_rgb_suave()
# --- FIM RGB BORDER SUAVE ---

if enable_bg:
    bg_img = Image.open(BACKGROUND_PNG).convert("RGBA")
    bg_img = ImageTk.PhotoImage(bg_img)
    canvas.create_image(0, 0, image=bg_img, anchor="nw")

canvas_width = 792
canvas_height = 491
center_x = canvas_width // 2
center_y = canvas_height // 2

def criar_bordas_arredondadas_rgb(largura, altura, raio, cor_borda, cor_fundo, espessura_borda):
    img = Image.new("RGBA", (largura, altura), (0, 0, 0, 0))
    draw = ImageDraw.Draw(img)
    # Borda externa
    draw.rounded_rectangle(
        (0, 0, largura, altura),
        radius=raio,
        fill=cor_borda
    )
    # Fundo interno
    draw.rounded_rectangle(
        (espessura_borda, espessura_borda, largura-espessura_borda, altura-espessura_borda),
        radius=raio-espessura_borda,
        fill=cor_fundo
    )
    return ImageTk.PhotoImage(img)

# Parâmetros do balão
largura_balao = 300
altura_balao = 50
raio_balao = 20
espessura_borda_balao = 6

# Variáveis para animação da borda RGB do balão
balao_cor_atual_idx = [0]
balao_passo_atual = [0]

def animar_borda_balao_rgb():
    idx = balao_cor_atual_idx[0]
    prox_idx = (idx + 1) % len(cores_rgb)
    t = balao_passo_atual[0] / passos
    cor = interpolate_color(cores_rgb[idx], cores_rgb[prox_idx], t)
    cor_hex = rgb_to_hex(cor)
    global rounded_bg_title_rgb
    rounded_bg_title_rgb = criar_bordas_arredondadas_rgb(
        largura_balao, altura_balao, raio_balao, cor_hex, "#ffffff", espessura_borda_balao
    )
    canvas.itemconfig(balao_canvas_id, image=rounded_bg_title_rgb)
    balao_passo_atual[0] += 1
    if balao_passo_atual[0] > passos:
        balao_passo_atual[0] = 0
        balao_cor_atual_idx[0] = prox_idx
    root.after(30, animar_borda_balao_rgb)

rounded_bg_title_rgb = criar_bordas_arredondadas_rgb(
    largura_balao, altura_balao, raio_balao, "#ff0000", "#ffffff", espessura_borda_balao
)
balao_canvas_id = canvas.create_image(center_x, center_y - 150, image=rounded_bg_title_rgb)
titulo = tk.Label(root, text="Senti Scanner 🔍", font=("Helvetica", 18, "bold"), bg="#ffffff")
canvas.create_window(center_x, center_y - 150, window=titulo)

animar_borda_balao_rgb()

rounded_bg_entry = criar_bordas_arredondadas(540, 160, 20, "#ffffff")
canvas.create_image(center_x, center_y - 20, image=rounded_bg_entry)
entrada_texto = tk.Text(root, height=8, width=58, font=("Helvetica", 12), bd=0, relief="flat", wrap="word", bg="#ffffff", highlightthickness=0)
canvas.create_window(center_x, center_y - 20, window=entrada_texto)

rounded_bg_button = criar_bordas_arredondadas(120, 40, 20, "#5cb85c")
canvas.create_image(center_x, center_y + 100, image=rounded_bg_button)
botao_analizar = tk.Button(root, text="Analisar", font=("Helvetica", 12, "bold"), fg="#ffffff", bg="#5cb85c", bd=0, relief="flat", command=verificar_sentimento)
canvas.create_window(center_x, center_y + 100, window=botao_analizar)

largura_resultado = 280
altura_resultado = 40
rounded_bg_result = criar_bordas_arredondadas(largura_resultado, altura_resultado, 20, "#ffffff")
canvas.create_image(center_x, center_y + 150, image=rounded_bg_result)
resultado = tk.Label(root, text="Descubra o sentimento", font=("Helvetica", 12, "bold"), bg="#ffffff")
canvas.create_window(center_x, center_y + 150, window=resultado)

rodape = tk.Label(root, text="Desenvolvido por Arthur and Ryk", font=("Helvetica", 10), bg="#000000", fg="#ffffff")
canvas.create_window(center_x, canvas_height - 10, window=rodape)

root.mainloop()
