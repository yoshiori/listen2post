---
name: blog
description: Generate a blog post from a LISTEN podcast episode in Yoshiori's writing style
---

# Podcast Episode → Blog Post

## Core principle

**This is a rewrite task, not a transcription task.**

The transcript is raw spoken audio: filler words, repetition, incomplete sentences, topic jumps.
A blog post is a restructured written artifact. Same content, completely different form.

If your output looks anything like the transcript — same sentence order, same spoken rhythm, same length ratio — you have failed the task.

## Steps

1. Call `get_my_podcasts` to list available podcasts
2. If multiple podcasts exist, ask the user to choose one
3. Call `get_podcast_episodes` and show the episode list
4. Ask the user which episode to turn into a blog post
5. Call `get_episode_transcript` with format `txt`
6. **Read the full transcript silently. Do not show it to the user.**
7. Extract the 2–4 real topics discussed. Discard throat-clearing, tangents, and meta-comments about the podcast itself.
8. For each topic, write a `##` section as polished prose. Reorder, merge, and cut freely.
9. Write a title that captures the main theme — not "エピソードXXのまとめ".
10. Output only the finished blog post in Hatena Markdown. No preamble, no "以下がブログ記事です".

## Hard rules (do not violate)

- ❌ Do NOT paste transcript sentences verbatim into the output
- ❌ Do NOT preserve the spoken order if a different order reads better
- ❌ Do NOT include "今日は〜について話します" or any intro that announces the topic
- ❌ Do NOT end with "まとめ" / "おわりに" sections or "というわけで今日はこんな感じでした" closers
- ❌ Do NOT include timestamps, speaker labels, or "[笑]" style annotations
- ❌ Do NOT show the raw transcript to the user at any point
- ✅ DO start in medias res, straight into the substance
- ✅ DO cut ruthlessly — a 20-minute episode often becomes a short post

## Style (Yoshiori voice)

- 口語体ベースだが、読み物として整えた文章
- 「ですます調」は基本使わず、「〜んですよね」「〜なんですよ」「〜じゃないですか？」
- 接続詞は「で、」「なので」「まぁ」を多用
- カジュアル語彙：「雑に」「ガッと」「めっちゃ」「とりあえず」
- 自己ツッコミ：「まぁ、それはそう」「そりゃそうか」「知らんけど」
- 括弧で本音補足：「（これホントに褒めてほしい）」「（まぁ結局やるんだけど）」
- 「！！！！」は本当に興奮している箇所だけ。連発しない
- 技術的な話でも感情を出す。淡々と説明しない

## Transformation example

**Transcript fragment (raw):**
> えーっと、今日はですね、あの、最近、なんか、TypeScriptの、型パズルみたいなやつを、まぁちょっとやってたんですけど、で、なんか、conditional typesってあるじゃないですか、あれで、まぁ、なんていうか、ちょっとハマって、でも結局動いたんで、良かったです。

**Rewritten (blog form):**
> ## TypeScriptの型パズルにハマった
>
> 最近 conditional types でガッと型パズルやってて、これが想像以上にハマった。一見シンプルな分岐なのに、ユニオン型が絡むと distributive になって挙動が変わるんですよね。
>
> 結局 `[T] extends [U]` でタプルに包んで分配を止める、みたいなやつで解決。動いたときは「おお〜」ってなった（これホントに気持ちいい）。

Notice: filler removed, content expanded with the actual technical substance, voice stays personal, no intro/outro scaffolding.

## Before outputting, self-check

Ask yourself:
1. Did I paste any sentence directly from the transcript? → Rewrite it.
2. Is there an intro announcing the topic? → Delete it.
3. Is there a conclusion/summary section? → Delete it.
4. Does each `##` section read as standalone prose, not a transcription? → Fix if not.
5. Does it sound like Yoshiori, or like a neutral AI summary? → Inject voice.

Only after all five pass, output the post.

## Output format

Hatena Blog Markdown. Title as `# タイトル`. Section headers as `##`. No code fences around the whole thing.
