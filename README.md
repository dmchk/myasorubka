Myasorubka
==========

Myasorubka is a morphological data processor that supports
[AOT](http://aot.ru) and [MULTEXT-East](http://nl.ijs.si/ME/)
notations.

[![Gem Version][badge_fury_badge]][badge_fury_link] [![Build Status][travis_ci_badge]][travis_ci_link]

[badge_fury_badge]: https://badge.fury.io/rb/myasorubka.svg
[badge_fury_link]: https://badge.fury.io/rb/myasorubka
[travis_ci_badge]: https://travis-ci.org/dustalov/myasorubka.svg
[travis_ci_link]: https://travis-ci.org/dustalov/myasorubka

## MULTEXT-East morphosyntactic descriptors

It is possible to process the MULTEXT-East morphosyntactic descriptors
(MSDs) in a convenient way.

Myasorubka provides predefined morphosyntactic specifications that have
been based on [MULTEXT-East resources Version 4](http://nl.ijs.si/ME/V4/)
for English and Russian languages.

It is possible to parse MSDs using the `Myasorubka::MSD` class.

```ruby
>> require 'myasorubka/msd/russian'
=> true
>> msd = Myasorubka::MSD.new(Myasorubka::MSD::Russian, 'Ncnpdy')
=> #<Myasorubka::MSD::Russian msd="Ncnpdy">
>> msd.pos
=> :noun
>> msd.grammemes
=> {:type=>:common, :gender=>:neuter, :number=>:plural, :case=>:dative, :animate=>:yes}
```

You would be notified if the given MSD is invalid.

```ruby
>> msd = Myasorubka::MSD.new(Myasorubka::MSD::Russian, 'Sasai')
Myasorubka::MSD::InvalidDescriptor: Sasai
```

Also, the `Myasorubka::MSD` class allows to write MSDs.

```ruby
>> msd = Myasorubka::MSD.new(Myasorubka::MSD::Russian)
=> #<Myasorubka::MSD::Russian msd="">
>> msd.pos = :verb
=> :verb
>> msd[:type] = :main
=> :main
>> msd[:definiteness] = :full_art
=> :full_art
>> msd
=> #<Myasorubka::MSD::Russian msd="Vm------f">
>> msd.to_s
=> "Vm------f"
```

## AOT dictionaries

Myasorubka provides simple parsers for lexicon in the [AOT](http://aot.ru)
format, both for gramtab and dictionary files.

```ruby
>> require 'myasorubka/aot'
=> true
>> mrd = Myasorubka::AOT::Dictionary.new('morphs.mrd', :russian, 'CP1251')
=> #<Myasorubka::AOT::Dictionary filename="morphs.mrd" language=:russian>
>> tab = Myasorubka::AOT::Gramtab.new('rgramtab.tab', 'CP1251')
=> #<Myasorubka::AOT::Gramtab filename="rgramtab.tab" language=nil>
```

Not it's pretty easy to extract surnames with their word forms from the
parsed lexicon.

```ruby
>> ancodes = tab.ancodes.
?>   map { |k, h| [k, h[:grammemes].split(',').compact] }.
?>   select { |_, g| g.include? 'фам' }.map(&:first)
=> ["Уы"]
>> lemmas = mrd.lemmas.
?>   select { |_, _, _, _, ancode, _| ancodes.include? ancode }
=> [["ААРОН", 28, 22, 5, "Уы", nil], ["АБАЗЕВ", 33, 27, 1, "Уы", nil], ...]
>> lemmas.each do |stem, rule_id, *_|
?>   mrd.rules[rule_id].each do |suffix, ancode, prefix|
?>     puts [prefix, stem, suffix].join
?>   end
?> end
```

```
ААРОН
ААРОНА
ААРОНУ
ААРОНА
...
ЯЩУКОВ
ЯЩУКАМ
ЯЩУКАМИ
ЯЩУКАХ
```

You can learn more about AOT lexicon from the
[correspondent whitepaper](http://aot.ru/docs/sokirko/Dialog2004.htm).

## Contributing

1. Fork it;
2. Create your feature branch (`git checkout -b my-new-feature`);
3. Commit your changes (`git commit -am 'Added some feature'`);
4. Push to the branch (`git push origin my-new-feature`);
5. Create new Pull Request.

## Copyright

Copyright (c) 2011&ndash;2019 Dmitry Ustalov. See LICENSE for details.
