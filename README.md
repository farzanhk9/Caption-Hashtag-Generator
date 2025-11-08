#!/usr/bin/env python3
# File: caption_hashtag_gen.py
# Generate catchy hooks, short captions, and mixed hashtags from a topic, tone, and audience.
# Pure Python. Works for Instagram, TikTok, Reels, Shorts, etc.

import argparse, random, re, unicodedata
from typing import List

EMOJIS = {
    "friendly": ["✨","🔥","💥","💡","😍","⚡","🙌","🎯","🛒","⭐"],
    "luxury":   ["💎","🎩","🌙","🖤","✨"],
    "tech":     ["🧠","⚙️","🚀","💡","📈","🔧"],
    "sport":    ["🏃","⚡","🔥","🏅","👟","💪"],
}

CTAS = [
    "Shop now", "Tap to see more", "DM us for details",
    "Save this for later", "Comment your favorite", "Tag a friend", "Link in bio"
]

def pick_emojis(tone: str, k=2) -> str:
    pool = EMOJIS.get(tone, EMOJIS["friendly"])
    return " ".join(random.sample(pool, k=min(k, len(pool))))

def normalize_kw(s: str) -> str:
    return re.sub(r"\s+", " ", s.strip())

def hashtag_mix(keywords: List[str], count=20) -> List[str]:
    kw = [re.sub(r"\s+", "", k.lower()) for k in keywords if k.strip()]
    big = ["fashion","style","viral","reels","explore","trending","unboxing","howto","sale","shopnow"]
    medium = []
    for c in kw:
        medium += [c, f"{c}daily", f"{c}tips", f"{c}review", f"{c}guide"]
    niche = []
    for c in kw:
        niche += [f"{c}community", f"{c}finds", f"{c}deals", f"{c}hack"]

    pool = random.sample(big, min(6,len(big))) + \
           random.sample(medium, min(7,len(medium))) + \
           random.sample(niche, min(12,len(niche)))

    out, seen = [], set()
    for t in pool:
        tag = "#"+t.replace("#","")
        if tag not in seen:
            out.append(tag); seen.add(tag)
        if len(out) >= count: break
    return out

def hooks(topic: str, tone: str) -> List[str]:
    e = pick_emojis(tone)
    return [
        f"{e} {topic.title()} that actually hits different.",
        f"Stop scrolling: {topic} done right.",
        f"Before you buy another one… try {topic}.",
        f"3 reasons {topic} is worth it.",
        f"{topic.title()} for people who hate compromises.",
        f"What nobody tells you about {topic}.",
        f"Upgrade unlocked → {topic}.",
        f"From meh to wow: {topic}.",
        f"Built to be noticed: {topic}.",
        f"Small change, massive result: {topic}.",
    ]

def captions(topic: str, tone: str, audience: str, tags: str) -> List[str]:
    e = pick_emojis(tone)
    b1 = f"• Built for {audience} — comfort without compromise."
    b2 = "• Clean design + durable build."
    b3 = "• Versatile for daily use."
    base = f"{e} {topic.title()} you’ll actually love.\n\n{b1}\n{b2}\n{b3}"
    return [
        f"{base}\n\n{random.choice(CTAS)} {pick_emojis(tone)}\n{tags}",
        f"{e} Real talk: {topic} done right.\n{b1}\n{b2}\n\n{random.choice(CTAS)}\n{tags}",
        f"From idea to everyday essential → {topic}.\n{b2}\n{b3}\n\n{random.choice(CTAS)}\n{tags}",
    ]

def generate(topic: str, tone: str, audience: str, keywords: List[str]):
    tone = tone.lower().strip() or "friendly"
    tags = " ".join(hashtag_mix(keywords, 20)) if keywords else " ".join(hashtag_mix([topic], 20))
    hk = hooks(topic, tone)
    caps = captions(topic, tone, audience, tags)

    print("=== HOOKS (10) ===")
    for i, h in enumerate(hk, 1): print(f"{i}. {h}")
    print("\n=== CAPTIONS (3) ===")
    for i, c in enumerate(caps, 1): print(f"\n[{i}]\n{c}")
    print("\n=== HASHTAGS (20) ===")
    print(tags)

def main():
    ap = argparse.ArgumentParser(description="Generate hooks, captions, and hashtags for social media posts.")
    ap.add_argument("--topic", required=True, help="Product or idea, e.g. 'handmade leather boots'")
    ap.add_argument("--tone", default="friendly", help="friendly|luxury|tech|sport")
    ap.add_argument("--audience", default="everyone")
    ap.add_argument("--keywords", default="", help="comma-separated keywords (optional)")
    args = ap.parse_args()

    kws = [normalize_kw(k) for k in args.keywords.split(",") if k.strip()]
    generate(args.topic.strip(), args.tone, args.audience, kws)

if __name__ == "__main__":
    main()

