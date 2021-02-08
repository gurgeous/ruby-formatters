# Ruby Formatter Roundup & Benchmarks

## The Ruby Formatting Landscape

I compared the top four Ruby code formatting tools. Note that except for RuboCop all of these tools rely on Ruby's built in Ripper for parsing.

- [Prettier for Ruby](https://github.com/prettier/plugin-ruby), a Ruby plugin for the widely used `prettier` tool, written in Javascript.
- [RuboCop](https://github.com/rubocop-hq/rubocop) is a hugely popular linter that also auto corrects. It has a deep understanding of Ruby.
- [Rubyfmt](https://github.com/penelopezone/rubyfmt) is written in Rust and blazingly fast, though under the hood it too relies on Ripper.
- [Rufo](https://github.com/ruby-formatter/rufo) tries to be unobtrusive. Development seems to have slowed a bit.

## Comparison & Benchmarks

I ran each tool against a set of test files. This repo contains branches so you can see the diff from each tool. Add `?w=1` to the diff to hide whitespace changes.

|                          | stars     | open issues | sample output                                                            |
| ------------------------ | --------- | ----------- | ------------------------------------------------------------------------ |
| Prettier for Ruby v2.2.1 | 1k stars  | 2           | [see diff](https://github.com/gurgeous/ruby-formatters/compare/prettier) |
| RuboCop v1.9.1           | 11k stars | 181         | [see diff](https://github.com/gurgeous/ruby-formatters/compare/rubocop)  |
| Rubyfmt v0.5.0           | 745 stars | 25          | [see diff](https://github.com/gurgeous/ruby-formatters/compare/rubyfmt)  |
| Rufo v0.12.0             | 678 stars | 19          | [see diff](https://github.com/gurgeous/ruby-formatters/compare/rufo)     |

Here are the test files I used:

|                                                                                                                                               | kb   | lines | [cloc](https://github.com/AlDanial/cloc/) |
| --------------------------------------------------------------------------------------------------------------------------------------------- | ---- | ----- | ----------------------------------------- |
| [active_support/core_ext/object/blank.rb](https://github.com/rails/rails/blob/main/activesupport/lib/active_support/core_ext/object/blank.rb) | 3k   | 155   | 57                                        |
| [action_view/helpers/form_helper.rb](https://github.com/rails/rails/blob/main/actionview/lib/action_view/helpers/form_helper.rb)              | 114k | 2651  | 406                                       |
| [active_record/relation/query_methods.rb](https://github.com/rails/rails/blob/main/activerecord/lib/active_record/relation/query_methods.rb)  | 53k  | 1573  | 767                                       |
| [redis-rb/redis.rb](https://github.com/redis/redis-rb/blob/master/lib/redis.rb)                                                               | 103k | 3541  | 1552                                      |

As a benchmark, I ran each formatter to compare speed (in seconds, averaged over 50 runs):

|                   | blank.rb | form_helper.rb | query_methods.rb | redis.rb |
| ----------------- | -------- | -------------- | ---------------- | -------- |
| Rubyfmt 🚀        | 0.02     | 0.03           | 0.03             | 0.05     |
| Rufo              | 0.14     | 0.18           | 0.18             | 0.24     |
| Prettier for Ruby | 0.56     | 0.75           | 0.72             | 1.03     |
| RuboCop 🦃        | 1.05     | 2.86           | 2.14             | 3.57     |

## Recommendations

I hesitate to recommend a specific formatter. I know that each project has a team of dedicated folks working hard to build something for the Ruby community. Being an open source maintainer is a difficult and thankless task. It's clear that a tremendous amount of work has gone into each project. A hearty THANK YOU to everyone involved.

#### RuboCop

RuboCop is the clear winner for workflow. Use RuboCop for linting and then fire it up again to format on save. Peanut butter and jelly! RuboCop will fix things that no other formatting tool will notice, like replacing `select.first` with `detect` for better performance. It can cut both ways, but usually the depth of RuboCop is a big benefit.

Unfortunately, my team has struggled to integrate RuboCop into VSCode. It's just too slow. Many times we've saved a Ruby file and then run something on the command line, only to find that RuboCop is still chugging away in the background. We tried [rubocop-daemon](https://github.com/fohte/rubocop-daemon), which solves the speed issue but opens a Pandora's box of complexity. The combination of VSCode, the VSCode Ruby extension, rubocop-daemon and RuboCop is impossible to debug and has a tendency to fail silently.

At the moment we use a hybrid setup. We have a wrapper script that runs rubocop-daemon for zippy VSCode integration, then a pre-commit hook for added certainty. We also have a cron job to check for formatting misses. This was not easy to set up.

#### Future

Prettier for Ruby is under active development and appears ready for production, though perhaps a tad slow. I suspect that my team will eventually move over to prettier, which we already use in other parts of the app. Prettier for Ruby even includes a handy plugin for turning off the RuboCop rules that conflict with formatting.

I would consider Rubyfmt as well, though it seems less mature than the other tools judging by the diffs above. Rufo is currently out of contention due to lack of recent activity.

#### Give Back

I make a small monthly donation to support the development of both [RuboCop](https://opencollective.com/rubocop) and [Prettier for Ruby](https://opencollective.com/prettier-ruby). This is an easy way to make our ecosystem better. If you liked this post and think Ruby formatting is important, consider donating to your favorite project.

Feb 2021
