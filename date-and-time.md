## `Date` / `Time` / `DateTime`

See https://thoughtbot.com/blog/its-about-time-zones

- Always use `Time`
- Always use UTC.

DON’T USE

- Time.now
- Date.today
- Date.today.to_time
- Time.parse("2015-07-04 17:05:37")
- Time.strptime(string, "%Y-%m-%dT%H:%M:%S%z")

DO USE

- Time.current
- 2.hours.ago
- Time.zone.today
- Date.current
- 1.day.from_now
