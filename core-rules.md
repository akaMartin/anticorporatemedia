# Core rules

These rules define the product. The data model and permission checks enforce them; the UI isn't the only safeguard.

Some rules cover features that ship after launch (tagging, data export). Don't build those early, but never design the data model in a way that would prevent them.

1. **Mutual friendship only.** A friendship exists only after a request is accepted. There is no follow, subscribe or other one-way relationship anywhere in the data model.
2. **Visibility follows the post.** A post is visible to its author's friends. Anyone who can see a post sees everything attached to it: all comments, tags, likes and like counts, whoever wrote them.
3. **Feed = friends' posts plus photos friends opted in to share (see 5), nothing else,** strict reverse-chronological, numbered pages (no infinite scroll), with a "you're all caught up" marker at the user's last visit. Older pages stay browsable.
4. **Tags in text and link posts notify only.** Tagging someone in a text or link post notifies that person. It does not show the post to anyone new.
5. **Photo tags** let the tagged person opt in to sharing the photo into their friends' feeds and onto their wall, credited to the original poster ("Ana's photo, shared by Ben"), entering feeds at the time of sharing. Nothing spreads without that opt-in. Viewers who reach it this way see all its comments/tags (rule 2).
6. **You can tag only your friends.** Tagged people can always remove their own tag.
7. **Non-friend profiles are a card only.** When you meet someone through a shared post, their profile shows name, photo, mutual friends and an "add friend" button, and none of their posts.
8. **Blocking overrides visibility.** If either person has blocked the other, neither sees anything the other wrote anywhere on the site: posts, comments, tags or profile, including comments on a mutual friend's post.
9. **No walled garden.** Users can export everything they created (posts, photos, comments, profile) in one click. Nothing authored by anyone else. Every model holding user-created content is included in the export, with a test.
