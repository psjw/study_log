# 🧠 복습 예정 카드 대시보드

```dataview
table review-due as "📅 복습 예정일", review-last-reviewed as "✅ 마지막 복습", file.link as "📘 주제"
from #flashcards
where review-due <= date(today)
sort review-due asc
```


```dataviewjs
const pages = dv.pages('"flashcards"');

const total = pages.length;

const due = pages.where(p => p["review-due"] <= dv.date("today")).length;

const percent = Math.round((due / total) * 100);

dv.paragraph(`📊 복습 진행률: ${percent}%`);

dv.paragraph(`<progress value="${percent}" max="100"></progress>`);
```
