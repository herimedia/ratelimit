Ratelimit: Slow your roll
==========================

[![Build Status](https://secure.travis-ci.org/ejfinneran/ratelimit.png?branch=master)](http://travis-ci.org/ejfinneran/ratelimit)


Ratelimit provides a way to rate limit actions across multiple servers using Redis.  This is a port of RateLimit.js found [here](https://github.com/chriso/redback/blob/master/lib/advanced_structures/RateLimit.js) and inspired by [this post](http://chris6f.com/rate-limiting-with-redis).

Usage
-----

My example use case is bulk processing data against an external API.  This will allow you to limit multiple processes across multiple servers as long as they all use the same Redis database.

Add to the count for a given subject via add with a unique key. I've used the example of a phone number below but anything unique would work (URL, email address, etc.)

You can then fetch the number of executions for given interval in seconds via the count method.

    ratelimit = Ratelimit.new("messages")
    5.times do
      ratelimit.add(phone_number)
    end
    ratelimit.count(phone_number, 30)
    => 5

You can check if a given threshold has been exceeded or not. The following code checks if the currently rate is over 10 executions in the last 30 seconds or not.

    ratelimit.exceeded?(phone_number, {:threshold => 10, :interval => 30})
    => false
    ratelimit.within_bounds?(phone_number, {:threshold => 10, :interval => 30})
    => true

You can also pass a block that will only get executed if the given threshold is within bounds. Beware, this code blocks until the block can be run.

    ratelimit.exec_within_threshold(phone_number, {:threshold => 10, :interval => 30}) do
      some_rate_limited_code
    end

Documentation
-------------

Full documentation can be found [here.](http://rubydoc.info/github/ejfinneran/ratelimit/frames)
