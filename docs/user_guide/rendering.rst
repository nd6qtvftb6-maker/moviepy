from moviepy.editor import *
import numpy as np

size = (540, 960)  # vertical
duration_total = 24

# Função para gerar bolhas animadas
def create_bubbles(duration, size, n_bubbles=20):
    def make_frame(t):
        frame = np.zeros((size[1], size[0], 3), dtype=np.uint8)
        for i in range(n_bubbles):
            # posição da bolha
            x = int((i*50 + t*50) % size[0])
            y = int((size[1] - ((i*40 + t*80) % size[1])))
            r = 15 + (i % 5)*3
            yy, xx = np.ogrid[:size[1], :size[0]]
            mask = (xx - x)**2 + (yy - y)**2 <= r**2
            frame[mask] = [255, 255, 255]  # branco
        return frame
    return VideoClip(make_frame, duration=duration)

# Função para criar cena com texto + fundo colorido + bolhas
def create_scene(text, duration, bg_color):
    txt_clip = TextClip(text, fontsize=50, color='yellow', size=(500, 900),
                        method='caption', align='center', font='Arial-Bold')
    bg_clip = ColorClip(size, color=bg_color, duration=duration)
    bubbles = create_bubbles(duration, size)
    return CompositeVideoClip([bg_clip, bubbles.set_opacity(0.4), txt_clip.set_position('center')]).set_duration(duration)

# Lista de cenas
scenes_data = [
    ("Atenção, marujos do fundo do mar! 🌊", 3, (135,206,250)),
    ("Vai ter uma festa super divertida! 🎈", 4, (255,182,193)),
    ("Yuri convida você pra comemorar com ele! 🧁", 4, (255,223,186)),
    ("27 de janeiro\nDas 19:00 às 23:00\nAvenida da Fraternidade, 1168 - Vila Olímpia\n(Maria João Park)", 8, (144,238,144)),
    ("Vai ser demais! Contamos com a sua presença! 💛", 4, (255,255,102)),
]

clips = [create_scene(text, dur, color) for text, dur, color in scenes_data]

# Cena final com botão WhatsApp
button_txt = TextClip("Clique aqui para confirmar ✅", fontsize=45, color='white', font='Arial-Bold')
button_bg = ColorClip(size, color=(0, 200, 0), duration=5)
button_bubbles = create_bubbles(5, size)
button_clip = CompositeVideoClip([button_bg, button_bubbles.set_opacity(0.4), button_txt.set_position('center')]).set_duration(5)
clips.append(button_clip)

# Concatenando tudo
final_clip = concatenate_videoclips(clips)

# Música (opcional)
# final_clip = final_clip.set_audio(AudioFileClip("musica_bob_esponja.mp3"))

# Exportando
final_clip.write_videofile("Convite_Yuri_BobEsponja.mp4", fps=24)
