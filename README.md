import re
from difflib import get_close_matches
import spacy

# Загрузка модели SpaCy для работы с семантикой
nlp = spacy.load("en_core_web_md")

def search(query_str, text):
    sentences = re.split(r'[.!?]', text)  # Разбиваем текст на предложения
    
    result = []
    for sentence in sentences:
        words = [token.text.lower() for token in nlp(sentence) if not token.is_stop and token.text.isalpha()]  # Убираем стоп-слова и пунктуацию
        query_words = [token.text.lower() for token in nlp(query_str) if not token.is_stop and token.text.isalpha()]  # Убираем стоп-слова и пунктуацию из запроса
        
        found_all_words = True
        
        for word in query_words:
            closest_word = get_close_matches(word, words, n=1, cutoff=0.6)  # Ищем самое похожее слово
            if closest_word:
                words.remove(closest_word[0])  # Удаляем найденное слово из списка, чтобы избежать повторений
            else:
                found_all_words = False
                break  # Если хотя бы одно слово из запроса не найдено, прекращаем поиск
        
        if found_all_words:
            result.append(sentence.strip())
    
    return result

# Пример использования:
file_path = 'C:\\hello.txt'
with open(file_path, 'r', encoding='utf-8') as file:
    text = file.read()

while True:
    query = input("Введите запрос: ")
    if not query:
        break
    result = search(query, text)
    print("Результат поиска:")
    for sentence in result:
        print(sentence)
