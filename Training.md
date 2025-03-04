# Trenowanie LLM (Large Language Model)

Napisałeś już swoje LLM, albo chcesz ulepszyć już istniejące AI? To czas na następny, z daleka niepozorny, krótki krok - trening. Zasady treningu i ogólne działanie są bardzo proste, jednak sam trening można nazwać monotonnym, zajmuje on dużo czasu oraz zasobów, a gdy podczas niego się okaże, że nasz kod jest niepoprawny, musimy to wszystko powtarzać od nowa.

Podczas czytania zalecam zobaczenie LLM napisanego przez jednego z nas. Jest on bardzo podstawowy, ale kod zawiera komentarze, oraz plik do trenowania. Trenowany jest tylko na jednym krótkim tekście, więc to pokazuje jak trenowanie jest ważne. [HeraMod na GitHub](https://github.com/BudzioT/HeraMod)  
Można przetestować na stronie [HeraMod na Hugging Face](https://huggingface.co/spaces/BartoszB/HeraMod)

---

## Zacznijmy od podstaw: po co trenować swoje LLM?

1. **Dostosowanie pod potrzeby**  
   Jest to prawdopodobnie jeden z najważniejszych powodów treningu. Pomijając fakt, że bez niego nasz model byłby bezużyteczny, to jednak z nim dostajemy możliwość dostosowania dzieła pod odbiorców, dany czas, zadanie i sposoby użycia.

2. **Efektywność kosztowa**  
   Budowanie własnego LLM i jego trening pozwala nam zaoszczędzić bardzo dużo pieniędzy. W końcu praktycznie każdy model opiera się na zasadzie "pay as you go", czyli kosztów zwiększających się wraz z użytkiem. Istnieją darmowe AI, ale przechodząc do efektywnych, szybkich API - istnieje bardzo mało darmowych wersji. Przykładami mogą być LLama 3.8 na Cloudflare lub na Groq. Oba oferują sensowną prędkość, jednak nadal jesteśmy bardzo limitowani pod względem dalszego rozwoju modelów, liczby zapytań. Sytuacją wymagającą bardzo dużego wkładu pieniędzy jest przykładowo system auto-completion. Zapytania wysyłane są czasami z każdym znakiem, a przy odpowiednim dostosowaniu, zoptymalizowaniu i używania cache, przy połowie słowa lub co każde słowo. Posiadanie lokalnego, własnego modelu początkowo jest bardzo kosztowne, ale suma zaoszczędzona rośnie wraz z użyciem, aż w końcu kompletnie spada drastycznie poniżej cen oprogramowań innych firm.

3. **Zmniejszenie zależności od dostawców**  
   Własne trenowanie pozwala na oddzielenie się od dostawców jak OpenAI, Google. Pozwala to na unikanie nagłych zmian polityki prywatności, płatności czy nowych ograniczeń do API. Stajemy się niezależni, a nasz model jest stabilny.

4. **Prywatność i bezpieczeństwo danych**  
   Własne trenowanie pomaga na zniwelowanie wycieków danych, w końcu sami w tym momencie dbamy o nasze bezpieczeństwo. Hakerzy głównie zajmują się większymi firmami, więc daje nam to większe poczucie bezpieczeństwa, a także dodatkową ochronę przed decyzjami administratorów tych usług. Pozwala to nam na trening i użycie LLM do prywatnych, wrażliwych dokumentów, ponieważ wiemy że są używane one tylko w zakresie naszej działalności. Pomaga to też używać ich zgodnie z regulacjami (GDPR, HIPAA itp.).

5. **Kontrola nad aktualizacjami, ulepszenia**  
   Własny model możemy trenować w każdej chwili na różnych danych, możemy w każdym momencie edytować plik z zapisem treningu, nadpisywać go lub kompletnie zamieniać. Pozwala to na ogromną wolność użytku i szerokie zastosowania. Również podczas napotkania błędów nie trzeba czekać na aktualizacje od sił wyższych, a sami możemy naprawić to co uległo zniszczeniu.

6. **Własność intelektualna**  
   Sami jesteśmy właścicielami naszego modelu. Możemy go trenować na czymkolwiek co posiadamy, posiadamy pełne prawo do kodu, rezultatów treningowych i wyników działania. Pomaga to też na ochronę modelu przed nieautoryzowanym użyciem np. przez konkurencję.

---

## Więc jak działa trenowanie? W najprostszym założeniu, są to dwa kroki:

1. **Pretraining**  
   Trenowanie na dużych, rozmaitych czystych zbiorach danych. Czyste w tym znaczeniu oznacza zwykły, nieformatowany/edytowany tekst. Dzięki niemu model zaczyna rozumieć nasz styl i język. Jest to podstawowy krok, a AI trenowane w taki sposób jest naprawdę podstawowe. Jest w stanie zapewnić podstawowe uzupełnianie tekstu na podstawie wcześniejszych danych oraz robić początkujące few-shot. Dzięki temu otrzymujemy podkład, który można dalej ulepszać. Przykładem LLM w takim stanie jest GPT-3 - który jest w stanie dokańczać teksty, ale ma też few-shot możliwości, czyli może robić nowe rzeczy bez potrzeby nauki na kolejnych ogromnych danych.

2. **Fine-tuning**  
   Proces w którym trenujemy LLM na specyficznych, mniej ogólnych i mniejszych zbiorach danych - które są dostosowane do tematów, zadań które chcemy by nasz model wykonywał. Dzięki niemu otrzymujemy model który jest w stanie klasyfikować rzeczy, streszczać je, tłumaczyć na inne języki a nawet możemy zbudować własnego personalnego asystenta. Fine-tuning możemy podzielić na instrukcyjne i klasyfikacyjne. Instrukcyjne polega na trenowaniu modelu dając mu instrukcje i odpowiedzi parami, np. tekst do przetłumaczenia i odpowiedź. W przypadku klasyfikacyjnym - dane zawierają tekst i opisy. Np. emaile z oznaczeniami "spam", "nie spam".

---

## Jednak to nie wszystko! Wdrażając się w szczegóły, tak naprawdę możemy dostrzec o wiele więcej procesów. Oto cały schemat:

### 1. Przetwarzanie wstępne (Preprocessing)
Przed rozpoczęciem trenowania modelu należy przygotować dane, które zostaną użyte do jego nauki. Proces ten obejmuje:

- **Zbieranie danych** – model LLM wymaga ogromnych zbiorów tekstowych, pochodzących np. z książek, artykułów, stron internetowych czy dokumentacji technicznej.
- **Czyszczenie danych** – usuwanie błędów, duplikatów, znaków specjalnych, a także filtrowanie treści niepożądanych lub niezgodnych z założeniami projektu.
- **Tokenizacja** – zamiana tekstu na mniejsze jednostki (tokeny), które model będzie analizował.
- **Normalizacja i standaryzacja** – np. konwersja wszystkich liter do małych, ujednolicenie formatu liczb, dat i symboli.
- **Tworzenie zbiorów danych** – podział na dane treningowe, walidacyjne i testowe, co pozwala na późniejszą ocenę skuteczności modelu.

### 2. Wstępne trenowanie modelu (Pretraining)
W tym momencie model nabiera dopiero sensu, uczy się o wszystkim, pochłania wiedzę. Model nie ma jeszcze konkretnego zastosowania – uczy się ogólnych wzorców językowych na podstawie ogromnych ilości tekstu.

- **Technika**: Używane są metody samonadzorowane (self-supervised learning), np.:
  - **Masked Language Modeling (MLM)** – ukrywanie losowych słów w zdaniu i próba ich odgadnięcia (stosowane w BERT).
  - **Causal Language Modeling (CLM)** – przewidywanie następnego słowa na podstawie wcześniejszych (np. GPT).
- **Efekt**: Model po tym etapie potrafi generować logiczne zdania, ale nie ma jeszcze specjalistycznej wiedzy ani zdolności do wykonywania konkretnych zadań.

### 3. Konfiguracja modelu (Model Configuration)
Przed rozpoczęciem właściwego trenowania konieczne jest ustalenie parametrów modelu, takich jak:

- **Architektura sieci neuronowej** – liczba warstw, typy warstw (np. Transformer), liczba neuronów w każdej warstwie.
- **Funkcja kosztu (loss function)** – określa, jak dobrze model przewiduje wyniki i jak bardzo trzeba go poprawić.
- **Optymalizator** – np. AdamW, który steruje procesem aktualizacji wag neuronów.
- **Rozmiar wsadu (batch size)** – liczba przykładów przetwarzanych jednocześnie podczas jednej iteracji.
- **Tempo uczenia się (learning rate)** – kontroluje, jak szybko model dostosowuje swoje parametry.

Konfiguracja ma ogromny wpływ na wydajność i skuteczność modelu – błędne ustawienia mogą prowadzić do przeuczenia (overfitting) lub niedouczenia (underfitting).

### 4. Trenowanie modelu (Model Training)
Jest to główny etap, w którym model aktualizuje swoje parametry na podstawie danych treningowych. Proces obejmuje:

- **Przekazywanie danych do modelu** – teksty są przekształcane w tokeny i podawane do warstw sieci neuronowej.
- **Obliczanie błędu predykcji** – model porównuje swoje przewidywania z rzeczywistymi danymi.
- **Aktualizacja wag** – optymalizator modyfikuje wagi neuronów, aby poprawić dokładność przewidywań.
- **Iteracje (epoki)** – model wielokrotnie przechodzi przez dane, stopniowo poprawiając swoje wyniki.

Proces ten jest bardzo zasobożerny – wymaga dużej mocy obliczeniowej (GPU, TPU) i często trwa dni lub tygodnie.

### 5. Dostosowanie do konkretnego zadania (Fine-tuning)
Po wstępnym treningu model może zostać dostosowany do konkretnej aplikacji. Istnieją dwa główne podejścia:

**a) Klasyfikacja (Classification Fine-Tuning)**

- Model dostaje zbiór danych oznaczonych kategoriami (np. spam vs. nie-spam, pozytywne vs. negatywne recenzje).
- Podczas treningu uczy się rozpoznawać wzorce i przypisywać teksty do odpowiednich klas.
- Przykłady zastosowań: filtrowanie treści, analiza sentymentu, klasyfikacja tematów.

**b) Trenowanie instruktażowe (Instructional Fine-Tuning)**

- Model uczy się podążać za instrukcjami, np. odpowiadać na pytania, streszczać teksty, generować kod.
- Wykorzystuje się technikę Reinforcement Learning from Human Feedback (RLHF), gdzie model jest oceniany i dostosowywany na podstawie preferencji ludzi.
- Przykłady zastosowań: chatboty, asystenci AI, modele generujące kod.

### 6. Ocena jakości modelu (Evaluation)
Po zakończeniu treningu model musi zostać dokładnie przetestowany, aby upewnić się, że działa zgodnie z oczekiwaniami.

- **Testowanie na zbiorach walidacyjnych i testowych** – sprawdzenie, jak dobrze model radzi sobie z danymi, których wcześniej nie widział.
- **Metryki oceny**:
  - **Perplexity (PP)** – mierzy, jak dobrze model przewiduje kolejne słowa (niższa wartość = lepszy model).
  - **Accuracy, Precision, Recall, F1-score** – stosowane w zadaniach klasyfikacyjnych.
  - **BLEU, ROUGE** – metryki oceniające jakość generowanego tekstu (np. podsumowań).
- **Testy praktyczne** – ocena przez użytkowników, zbieranie opinii, analiza błędów i niedoskonałości.

Na podstawie wyników model może zostać poprawiony poprzez dodatkowy trening, zmianę hiperparametrów lub filtrowanie danych treningowych.

---

## Skoro już mamy schemat za sobą, to powiedzmy sobie o ogólnym działaniu. Jak to jest możliwe, że LLM się uczą?

Trenowanie modelu językowego (LLM) to proces, w którym model uczy się rozumieć język i generować tekst na podstawie ogromnych zbiorów danych. W tym procesie kluczowe są etapy przekształcania surowego tekstu w formę zrozumiałą dla sieci neuronowej, analiza wzorców oraz optymalizacja parametrów modelu.

### 1. Przetwarzanie tekstu – od surowych danych do tokenów

Zanim model zacznie trenować, tekst musi zostać przekształcony w formę, którą sieć neuronowa jest w stanie analizować. Ten etap obejmuje:

**a) Tokenizacja**

- Tekst jest dzielony na mniejsze jednostki, zwane tokenami.
- Tokeny mogą reprezentować pojedyncze znaki, całe słowa lub ich fragmenty.
- Popularne techniki tokenizacji:
  - **WordPiece** – dzieli słowa na mniejsze segmenty (np. "running" → "run" + "##ning").
  - **Byte-Pair Encoding (BPE)** – łączy najczęściej występujące pary znaków w większe jednostki.
  - **SentencePiece** – pozwala na tokenizację niezależną od spacji i znaków interpunkcyjnych.

**b) Enkodowanie tokenów**

- Każdy token jest zamieniany na liczbę (ID) na podstawie słownika modelu.
- Te ID są następnie konwertowane na wektory liczbowe w tzw. osadzeniach (word embeddings), które reprezentują znaczenie słów.
- Model nie „widzi” tekstu jako znaków, lecz jako zestawy liczb w przestrzeni wielowymiarowej.

**c) Uzupełnianie i obcinanie (Padding & Truncation)**

- Ponieważ modele LLM działają na fragmentach tekstu o określonej długości, krótsze zdania są uzupełniane specjalnym tokenem (<PAD>), a dłuższe są przycinane.
- Zapewnia to spójny kształt wejściowych danych, co jest istotne przy przetwarzaniu w partiach (batch processing).

### 2. Przepływ danych przez sieć neuronową

Po przekształceniu tekstu w tokeny model LLM przetwarza je za pomocą warstw sieci neuronowej – zwykle architektury **Transformer**.

**a) Warstwa osadzeń (Embedding Layer)**

- Tokeny są zamieniane na wektory liczbowe (osadzenia), które reprezentują ich znaczenie w danej przestrzeni semantycznej.
- Każde słowo jest reprezentowane przez wielowymiarowy wektor, który koduje jego znaczenie i relacje z innymi słowami.

**b) Mechanizm uwagi (Self-Attention)**

- Model analizuje zależności między słowami w zdaniu, niezależnie od ich pozycji.
- **Self-Attention** (mechanizm samo-uwagi) pozwala modelowi wykrywać kontekst – np. w zdaniu „Bank na rzece był szeroki” model rozumie, że „bank” oznacza brzeg, a nie instytucję finansową.
- Każde słowo otrzymuje tzw. **wagi uwagi**, które określają, jak bardzo jest istotne w kontekście zdania.

**c) Normalizacja i aktywacja**

- Po przetworzeniu przez warstwy uwagi dane przechodzą przez mechanizmy **normalizacji warstwowej (Layer Normalization)** oraz **funkcje aktywacji**, np. ReLU lub GELU, które pomagają modelowi lepiej uczyć się nieliniowych zależności w języku.

**d) Dekoder i generacja tekstu**

- W modelach generatywnych (np. GPT) dekoder przewiduje kolejne tokeny na podstawie wcześniej wygenerowanych.
- Używa do tego funkcji **Softmax**, która oblicza prawdopodobieństwo wystąpienia każdego tokena jako następnego słowa w zdaniu.
- Model może generować tekst **token po tokenie**, dobierając najbardziej prawdopodobne słowa na podstawie wcześniejszych.

### 3. Trenowanie modelu – uczenie się wzorców językowych

Podczas treningu model przechodzi przez miliony przykładów tekstu, stopniowo dostosowując swoje parametry, aby poprawnie przewidywać kolejne słowa lub wykonywać inne zadania językowe. Proces ten jest kluczowy dla osiągnięcia przez model wysokiej jakości generowania tekstu, rozumienia kontekstu i wykonywania specyficznych zadań.

## 3. Trenowanie modelu – uczenie się wzorców językowych (ciąg dalszy)

### a) Funkcja straty (Loss Function)

- Model porównuje swoje przewidywania z rzeczywistymi danymi i oblicza, jak duży był błąd.
- **Popularne funkcje straty**:
  - **Cross-Entropy Loss** – mierzy różnicę między prawdopodobieństwem przypisanym poprawnym tokenom a rzeczywistą dystrybucją tokenów w danych treningowych.
  - **Perplexity (PP)** – mierzy, jak dobrze model przewiduje kolejne słowa (niższa wartość = lepszy model).

### b) Optymalizacja i aktualizacja wag

- Model dostosowuje swoje parametry (wagi neuronów), aby minimalizować funkcję straty.
- Stosuje się do tego **algorytmy optymalizacyjne**, np. AdamW, który modyfikuje wartości wag na podstawie gradientów błędu.
- Proces ten jest powtarzany przez wiele **epok** – każda epoka to jedno pełne przejście przez cały zbiór danych.

### c) Regularizacja

- Aby uniknąć przeuczenia (overfitting), stosuje się techniki regularizacji, takie jak:
  - **Dropout** – losowe wyłączanie neuronów podczas treningu, aby zapobiec nadmiernemu dopasowaniu do danych treningowych.
  - **Weight Decay** – dodanie kary do funkcji straty za zbyt duże wartości wag, co zapobiega nadmiernemu wzrostowi parametrów.

---

## 4. Generacja i ocena modelu

Po zakończeniu treningu model może generować tekst, ale jego jakość musi być oceniona i ewentualnie poprawiona.

### a) Strategie generacji tekstu

- **Greedy Decoding** – model zawsze wybiera najbardziej prawdopodobny token, co może prowadzić do monotonnych wyników.
- **Beam Search** – model analizuje kilka możliwych ścieżek generacji i wybiera tę z najwyższym prawdopodobieństwem.
- **Top-k Sampling** – model losowo wybiera spośród **k** najbardziej prawdopodobnych tokenów, co zwiększa różnorodność odpowiedzi.
- **Temperature Scaling** – kontroluje losowość generacji (wyższa wartość = bardziej kreatywne odpowiedzi).

### b) Ocena jakości modelu

- **Testy na zbiorach walidacyjnych** – sprawdzenie, jak dobrze model radzi sobie z nowymi danymi.
- **Metryki jakości**:
  - **BLEU, ROUGE** – ocena podobieństwa wygenerowanego tekstu do tekstów referencyjnych.
  - **Human Evaluation** – testy jakościowe przez ludzi, którzy oceniają, czy teksty są sensowne i poprawne.

---

## Jak są zapisywane wyniki? Jak je możemy wczytać?

Pamięć operacyjna nie jest zapisywana na dysku twardym, więc wyniki treningu muszą być odpowiednio przechowywane, aby można je było później wykorzystać.

### 1. Zapisywanie modelu po treningu

#### a) Co dokładnie zapisujemy?

Podczas trenowania model aktualizuje setki milionów, a nawet miliardy parametrów. Plik zapisujący model może zawierać:

- **Wagi modelu (model weights)** – czyli macierze liczbowe, które model nauczył się podczas treningu. Przechowują informacje o zależnościach między tokenami i reprezentacje semantyczne słów.
- **Architekturę modelu** – informacje o warstwach sieci neuronowej (liczba warstw, liczba neuronów w każdej warstwie, funkcje aktywacji itp.).
- **Konfigurację modelu** – parametry takie jak liczba tokenów wejściowych, maksymalna długość sekwencji czy strategia maskowania tokenów.
- **Optymalizator i jego stan** – jeśli chcemy wznowić trening, zapisujemy także stan optymalizatora (np. AdamW), w tym bieżące wartości gradientów i tempa uczenia.

#### b) Format zapisu

Model może być zapisany w różnych formatach w zależności od użytej biblioteki i celu użycia:

- **PyTorch (.pt, .bin, .pth)** – popularny format dla modeli używanych w PyTorch.
- **TensorFlow (.h5, SavedModel)** – format dla modeli trenowanych w TensorFlow/Keras.
- **ONNX (.onnx)** – uniwersalny format kompatybilny z różnymi frameworkami.
- **Hugging Face Transformers (.bin, config.json)** – standardowy format dla modeli językowych udostępnianych przez bibliotekę 🤗 Transformers.

**Przykładowy naprawdę prosty kod zapisu w PyTorch:**

```
import torch

# Przykładowy model PyTorch
model = MyNeuralNet()

# Zapisz wagi modelu
torch.save(model.state_dict(), "model_weights.pth")

# Zapisz cały model (architekturę + wagi)
torch.save(model, "full_model.pth")
```
2. Wczytywanie modelu do użycia
Po zapisaniu modelu możemy go wczytać w innym środowisku lub kontynuować trening.

a) Wczytanie modelu w PyTorch
```
import torch

# Wczytanie tylko wag modelu
model = MyNeuralNet()
model.load_state_dict(torch.load("model_weights.pth"))
model.eval()  # Przełącz na tryb ewaluacji

# Wczytanie całego modelu
model = torch.load("full_model.pth")
model.eval()
```
b) Wczytanie modelu Hugging Face
```
from transformers import AutoModel, AutoTokenizer

model_name = "bert-base-uncased"
model = AutoModel.from_pretrained(model_name)
tokenizer = AutoTokenizer.from_pretrained(model_name)

# Modele z Hugging Face można też pobrać z własnych checkpointów
model = AutoModel.from_pretrained("./moj_model/")
tokenizer = AutoTokenizer.from_pretrained("./moj_model/")
```
3. Używanie modelu po wczytaniu
Po wczytaniu modelu można wykorzystać go do generowania tekstu, klasyfikacji, tłumaczenia itp.

a) Generacja tekstu z GPT-2
```
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

input_text = "Once upon a time"
input_ids = tokenizer(input_text, return_tensors="pt").input_ids

output = model.generate(input_ids, max_length=50)
print(tokenizer.decode(output[0], skip_special_tokens=True))
```
b) Klasyfikacja tekstu za pomocą BERT
```
from transformers import AutoModelForSequenceClassification, AutoTokenizer

model = AutoModelForSequenceClassification.from_pretrained("distilbert-base-uncased-finetuned-sst-2-english")
tokenizer = AutoTokenizer.from_pretrained("distilbert-base-uncased-finetuned-sst-2-english")

text = "This movie was amazing!"
inputs = tokenizer(text, return_tensors="pt")
outputs = model(**inputs)
print(outputs.logits)  # Wyniki klasyfikacji (np. sentymentu)
```
4. Checkpointy – zapisywanie modelu w trakcie treningu
Podczas długiego treningu często zapisuje się model w regularnych odstępach czasu, aby uniknąć utraty postępów (np. przy awarii).

a) Zapisywanie checkpointów
```
import torch

checkpoint = {
    'epoch': 10,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'loss': loss_value
}

torch.save(checkpoint, "checkpoint.pth")
```
b) Wczytywanie checkpointów
```
checkpoint = torch.load("checkpoint.pth")
model.load_state_dict(checkpoint['model_state_dict'])
optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
epoch = checkpoint['epoch']
loss = checkpoint['loss']
```

---

## Ale LLM nie używa się tylko do tekstu. Przecież istnieją video, audio wygenerowane przez AI.

Dokładnie, ale ogólny schemat wygląda tak samo. Poniżej można przeczytać kilka przykładów treningu w zależności od rodzaju danych.

---

### 1. Trenowanie LLM na tekście (Natural Language Processing, NLP)

Modele trenowane na tekście są wykorzystywane w zadaniach takich jak generowanie tekstu, tłumaczenie, analiza sentymentu, streszczanie dokumentów itp.

#### a) Dane treningowe

- **Korpusy tekstowe** – Wikipedia, książki, strony internetowe, dokumenty.
- **Dane specjalistyczne** – artykuły naukowe, dokumenty prawne, literatura techniczna.
- **Dialogi** – dane z chatbotów, konwersacje ze wsparcia technicznego.

#### b) Tokenizacja i preprocessing

- Tekst jest dzielony na tokeny (subword, słowa lub całe zdania).
- **Popularne metody tokenizacji**:
  - **WordPiece (BERT, GPT)**
  - **Byte Pair Encoding (BPE, GPT-2, LLAMA)**
  - **SentencePiece (T5, mT5, ALBERT)**

#### c) Model i trening

- **Architektura** – transformery (GPT, BERT, T5).
- **Pretraining** – modele uczą się przewidywać kolejne słowa (causal language modeling) lub maskowane słowa (masked language modeling).
- **Fine-tuning** – model jest dopasowywany do konkretnego zadania, np. analiza sentymentu.

---

### 2. Speech-to-Text (STT) – Rozpoznawanie mowy

Modele Speech-to-Text (STT) konwertują sygnał dźwiękowy na tekst. Są używane w transkrypcji rozmów, systemach asystentów głosowych i napisach do filmów.

#### a) Dane treningowe

- **Nagrania mowy** – podcasty, rozmowy telefoniczne, audiobooki.
- **Parowane dane audio-tekst** – zdanie w formie nagrania oraz jego transkrypcja.

#### b) Preprocessing

- Konwersja dźwięku do formatu spektrogramu (np. MFCC, Mel spectrogram).
- Normalizacja głośności i usuwanie szumów.
- Tokenizacja transkrypcji tekstowej.

#### c) Model i trening

- **Architektura** – często połączenie CNN + RNN/Transformer (np. Whisper, Wav2Vec 2.0, DeepSpeech).
- **Trening** – model uczy się mapować dane audio na tekst, wykorzystując techniki CTC (Connectionist Temporal Classification) lub seq2seq.

#### d) Fine-tuning

- Dostosowanie modelu do konkretnego języka, akcentu lub dziedziny (np. medycznej terminologii).

---

### 3. Text-to-Speech (TTS) – Generowanie mowy z tekstu

Modele TTS konwertują tekst na mowę, co znajduje zastosowanie w syntezatorach mowy, audiobookach, asystentach głosowych itp.

#### a) Dane treningowe

- **Parowane próbki tekst-audio** – tekst + nagranie jego wymowy.
- **Nagrania lektorów** – wysokiej jakości próbki głosu profesjonalnych lektorów.

#### b) Preprocessing

- Tokenizacja tekstu.
- Konwersja nagrań do spektrogramów.
- Usuwanie zakłóceń i normalizacja audio.

#### c) Model i trening

- **Architektura** – modele seq2seq lub transformery (Tacotron 2, FastSpeech, VITS).
- **Trening** – model uczy się przewidywać spektrogram na podstawie tekstu, a następnie model vocoder (np. WaveNet, HiFi-GAN) konwertuje spektrogram na falę dźwiękową.

---

### 4. Trenowanie LLM na obrazach – Vision Transformers (ViT, CLIP)

LLM można trenować także do przetwarzania obrazów, np. w zadaniach opisu obrazów, analizy wizualnej, wykrywania obiektów.

#### a) Dane treningowe

- **Zbiory obrazów** – ImageNet, COCO, OpenImages.
- **Parowane obrazy z tekstem** – CLIP (OpenAI) wykorzystuje obrazy i podpisy do nauki powiązań między wizją a językiem.

#### b) Preprocessing

- Skalowanie obrazów do jednolitych rozmiarów.
- Normalizacja pikseli.
- Tokenizacja podpisów obrazów (jeśli model działa w trybie multimodalnym).

#### c) Model i trening

- **Architektura** – Vision Transformers (ViT), CNN (ResNet), multimodalne modele (CLIP, BLIP).
- **Trening** – model uczy się wyodrębniać cechy wizualne i dopasowywać je do tekstu (w przypadku CLIP) lub przewidywać obiekty.

---

### 5. Trenowanie LLM na wideo

Modele przetwarzające wideo są stosowane w analizie filmów, rozpoznawaniu obiektów, generowaniu napisów itp.

#### a) Dane treningowe

- **Bazy danych wideo** – Kinetics, ActivityNet, YouTube-8M.
- **Parowane napisy z wideo** – do nauki generowania opisów.

#### b) Preprocessing

- Podział wideo na klatki.
- Ekstrakcja cech wizualnych (CNN, ViT).
- Przekształcanie dźwięku na spektrogram dla analizy audio.

#### c) Model i trening

- **Architektura** – CNN + RNN, Vision Transformers + Temporal Transformers (np. TimeSformer).
- **Trening** – model uczy się przewidywać akcję w klipie, generować napisy lub tłumaczyć ruch wideo na opis tekstowy.

---

### 6. Multimodalne modele – przetwarzanie różnych typów danych jednocześnie

Niektóre modele LLM są trenowane na różnych typach danych jednocześnie, np.:

- **CLIP (OpenAI)** – rozumie powiązania między obrazami a tekstem.
- **DALL·E** – generuje obrazy na podstawie tekstu.
- **Flamingo (DeepMind)** – przetwarza tekst i obrazy, ucząc się odpowiadać na pytania dotyczące obrazów.

#### a) Trenowanie multimodalnych modeli

- Dane składają się z par obraz-tekst, audio-tekst itp.
- Model używa kilku enkoderów (np. CNN do obrazów, Transformer do tekstu).
- Zastosowanie: chatboty wizualne, generowanie obrazów, wyszukiwanie semantyczne.

---

## Jak wygląda AI bez treningu? Tragicznie.

Z mojego doświadczenia mogę powiedzieć, że nawet trening 8-godzinny na 500-stronicowym tekście (GTX 1060 6GB VRAM, 16 GB RAM) jest wielkim progresem w porównaniu do treningu 5-minutowego lub żadnego. Przykładowe odpowiedzi przed treningiem wyglądały w miarę podobnie do:

- **Prompt**: "What is that?"  
  **Uzupełnienie**: "What is that? And and and and and and and"

- **Prompt**: "What is that?"  
  **Uzupełnienie**: "What is that? , . , . and or , , but , . , . . ."

Nowe odpowiedzi można zobaczyć w zdjęciach podanych w readme repozytorium znajdującego się na początku tego tekstu.

---

## Podsumowanie

Trening LLM jest tak naprawdę najważniejszą częścią, nawet stojącą blisko samemu napisaniu kodu. W końcu niewytrenowany model, nawet dobrze napisany, nic nie zrobi. Proces ten wymaga czasu, zasobów i cierpliwości, ale efekty są warte wysiłku. Dzięki treningowi model staje się użyteczny, dostosowany do konkretnych zadań i niezależny od zewnętrznych dostawców.
