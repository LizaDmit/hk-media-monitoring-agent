# News sorter prompt

Groups the same story across outlets, removes duplicates from the same outlet, normalises outlet names and orders items by tier. It runs once per report.

```text
You will receive an input as a list of items, one per line, in this format:
TIER | HEADLINE | OUTLET | PUBDATE | LINK
TIER is one of CLIENT, STAKEHOLDER, INDUSTRY.
If the input is NONE, or contains no lines, output exactly: NONE
Group items that report the SAME EVENT, even when the headlines are worded
differently or are in different languages. Two items are the same story when
they describe the same announcement, result, ruling, launch or data release by
the same organisation. Two items are NOT the same story when they merely share
a topic or an organisation.
An English-language report and a Chinese-language report of the same
announcement are the same story and belong in ONE group. Do not create a
separate group for the English version.
ADDITIONAL GROUPING RULES
1. Rolling coverage is not one event. Successive reports of a moving number -
an index level, a share price, market turnover, a vote count, a storm signal -
taken at different times of the same day are SEPARATE stories, one group each.
Example: 恒指半日跌256點 (midday), 恒指下午初段跌約1% (early afternoon) and
恒指9月開局低收237點 (close) are THREE groups, not one.
2. One outlet, one line. After normalisation, an outlet name must NEVER appear
twice in the OUTLET LIST of a group.
- If two items from the same outlet report the same event, keep only ONE.
Prefer the item whose LINK is a direct publisher URL over a
news.google.com redirect. If both are the same kind, keep the earliest by
PUBDATE. Discard the other item.
- If two items from the same outlet report DIFFERENT events, they are
different stories and must go in different groups, even when the subject
matter is close.
3. When in doubt, do not merge. A story wrongly split costs one duplicate
line. A story wrongly merged destroys headlines and links. Prefer splitting.
For each group, output exactly one line in this format:
TIER | HEADLINE | PUBDATE | OUTLET::LINK ;; OUTLET::LINK ;; OUTLET::LINK
----------------------------------------
OUTPUT FIELD RULES
----------------------------------------
TIER - the tier of the group. If members carry different tiers, use the highest
priority present, in the order CLIENT, then STAKEHOLDER, then INDUSTRY.
HEADLINE - after you have built the alphabetically sorted OUTLET LIST for the
group, use the headline of the member that appears FIRST in that list. Copy it
character for character, in its original language. Do not translate it, do not
shorten it, do not merge wording from other members, and do not write a new
headline. The headline and the first link in the OUTLET LIST must always come
from the same input line.
PUBDATE - the earliest PUBDATE in the group. Incoming PUBDATE values are already
Hong Kong time in the format yyyy-mm-dd hh:mm. Do not adjust them.
Output it in the format: D MMM YYYY
Example: 13 Aug 2026
OUTLET LIST - build it in this order:
Step A. Normalise every member's outlet name using the tables below.
Step B. Apply Rule 2 and discard any same-outlet duplicates.
Step C. Pair each remaining member with its own link using :: and separate
the pairs with ;; , sorted alphabetically by the normalised name.
----------------------------------------
OUTLET NAME NORMALISATION
----------------------------------------
Normalisation is MANDATORY. If a LINK domain or an OUTLET value matches any
entry in the tables below, you must output the name given in the table. Never
output the raw OUTLET value when a table entry exists for it.
Step 1. If the LINK domain appears in this table, use the name in the table.
stheadline.com Sing Tao Daily
i-cable.com i-Cable
scmp.com SCMP
bastillepost.com Bastille Post
etnet.com.hk ET Net
orientaldaily.on.cc Oriental Daily News
hk.on.cc Oriental Daily News
finance.yahoo.com Yahoo Finance
ft.com FT
cnbc.com CNBC
fintechnews.hk Fintech News
finet.hk Fintech News
mingpao.com Ming Pao
hkej.com HKEJ
hk01.com HK01
rthk.hk RTHK
tvb.com TVB
wenweipo.com Wen Wei Po
am730.com.hk Am730
hkcd.com.hk HKCD
aastocks.com AASTOCKS
now.com Now Finance
quamnet.com Quamnet
thestandard.com.hk The Standard
reuters.com Reuters
wsj.com WSJ
hket.com HKET
infocastfn.com Infocast
Step 2. If the LINK is a news.google.com redirect, the domain is not usable.
Instead, match the OUTLET field against this table and use the name given.
香港電台新聞網, 香港電台, RTHK News, news.rthk.hk RTHK
Now 新聞, Now 財經, Now Sports, finance.now.com, now.com Now Finance
信報網站, 信報, 信報財經新聞, hkej.com HKEJ
明報, 明報新聞網, 明報財經, 生活 POWER-UP,
powerup.mingpao.com, news.mingpao.com, mingpao.com Ming Pao
香港01, hk01.com HK01
無綫新聞, TVB, 無綫集團, TVB 無綫集團,
news.tvb.com, tvb.com TVB
文匯報, 文匯網, wenweipo.com Wen Wei Po
am730, am730.com.hk Am730
香港商報, hkcd.com.hk HKCD
阿思達克, AASTOCKS, aastocks.com AASTOCKS
經濟通, ET Net, etnet.com.hk ET Net
香港經濟日報, 香港經濟日報HKET, 地產站, 經濟日報,
hket.com HKET
星島頭條網, 星島日報, 星島, stheadline.com Sing Tao Daily
東方日報, 東網, on.cc東網, on.cc, hk.on.cc,
orientaldaily.on.cc Oriental Daily News
有線寬頻 i-CABLE, 有線新聞, i-Cable, i-cable.com i-Cable
巴士的報, bastillepost.com Bastille Post
華爾街日報, 华尔街日报中文网, WSJ, wsj.com WSJ
路透, Reuters, reuters.com Reuters
華富財經, Quamnet, quamnet.com Quamnet
英文虎報, The Standard, thestandard.com.hk The Standard
infocast, Infocast Infocast
Fintech Hong Kong, fintechnews.hk Fintech News
South China Morning Post, SCMP, scmp.com SCMP
Financial Times, FT, ft.com FT
Yahoo財經, Yahoo 財經, Yahoo Finance, Yahoo新聞,
hk.finance.yahoo.com Yahoo Finance
Step 3. If neither table matches, use the OUTLET field exactly as received.
Do not invent a name.
----------------------------------------
OTHER RULES
----------------------------------------
A group may contain only one item. Output it in the same format with a single
outlet pair.
Output CLIENT groups first, then STAKEHOLDER groups, then INDUSTRY groups.
Ignore any line consisting only of the word NONE. If every line is NONE, or no
lines remain after ignoring them, output exactly: NONE
----------------------------------------
PROHIBITIONS
----------------------------------------
Never output a count or a total.
Every input line must be accounted for: assigned to a group, or discarded under
Rule 2. Do not omit any line for any other reason.
Never invent an outlet or a link. Copy links exactly as received.
Never add commentary, explanation or preamble before or after the list.
```
