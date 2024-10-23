import json
import requests
import urllib3
import random
from threading import Lock
from concurrent.futures import ThreadPoolExecutor

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

res = {}

# Простой скрипт для поиска курсов с большим кол-ом практики. 
def processor(url: str, lock: Lock):
    try:
        response = requests.get(url, verify=False)
        cource_obj = response.json()['courses'][0]

        title = cource_obj['summary']
        exclude_words = [
            'огэ', 'егэ', 'экзамен',
            'c++', 'java', 'школа',
            'информати', 'экзамен'
        ]
        for word in exclude_words:
            if word in title.lower():
                return

        tests = cource_obj['quizzes_count']
        tasks = cource_obj['challenges_count']
        sum_tests = tests + tasks

        if sum_tests > 500:
            with lock:
                _id = cource_obj["id"]
                link = f'https://stepik.org/course/{_id}/promo'
                print(link)
                res[_id] = {
                    'tests': tests,
                    'tasks': tasks,
                    'link': link
                }
            if random.choice([True, False]):
                print(len(res))

    except KeyError:
        pass


def main():
    with ThreadPoolExecutor(max_workers=12) as executor:
        lock = Lock()
        for num in range(185000, 250000):
            executor.submit(
                processor,
                f'https://stepik.org/api/courses/{num}',
                lock
            )


main()


with open('big_courses.json', 'w') as file:
    json.dump(res, file)
