# Exante Code test

## To run this

Assuming just dev mode as this is a code test 🫡

I'd usually use yarn, but don't want to make assumptions about your setup.

```sh
npm install
```

```sh
npm run dev
```

## My Assumptions about the component/data

I've made a few, they may be incorrect, but i couldn't figure out by asking you guys given it's a weekend. here they are.

- current period is either now minus 6 months or between the start of contract and now, whichever is more current. I would assume the minus 6 months is likely incorrect and you only provide this service for the first six months of a contract and not on a rolling 6 monthly basis.

- if a user has a partial month, i have applied the expected amount for the whole month. This likely skewed their percentage down. for example, the user contract provided starts on the 15th August. I could have taken Augusts expectation and split it by the number of days in the month then multiplied by the amount of days they were under contract that month (15th - 31st). this would have been equally wrong as all days dont have a uniform amount of sun, so i opted for the more blanket solution. in reality, i'd have gone back to the person who designed this to see what the intent was.

- i have assumed the expected values are the values expected for _that_ calendar month. eg: December 1st is for the month of December. again this may skew the details given it's currently december 2nd so i'll have applied decembers expected amount, but have no user data beyond november 29th.

- I have assumed the three tags on the gauge (left, right, centre) change based on the percentage. so it will always show users percentage `power of 10-1` on the left, the `power of 10` in the centre and `power of 10+1` on the right, EG: for 74 it will show 60 on left, 70 in the center and 80 on right, for 110 itll be 100,110,120. I tried always keeping 100 in the middle like in the screenshot in case my assumption was incorrect but it looked and felt odd. Again, this is one i'd normally chase up for the intent but i think it works ok this way.

- I haven't done much with the layout. would definitely want to do something below 320px if this was going to production but that would require some design decisions to cater to this size, which your users may not ever use. have garnered what i can about font, radiuses, borders, spacing etc from the screenshot. they may not be 100% correct.
