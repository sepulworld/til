# Set State from Client Side

With a system like [Riemann](https://riemann.io/) it can be handy to set and forward state information to configured
Riemann streams. Here is an example snippet of a feed using Ruby with the [Riemann client gem](https://github.com/riemann/riemann-ruby-client)

```ruby
require 'riemann/client'
 
c = Riemann::Client.new host: 'my.riemann-server.com', port: 5555, timeout: 5
 
def do_something_to_get_metric() 
  # code to set metric for push notifications sent for 5min period 
end

def define_state()
  # code to define what are known peak state is and returns 'non-peak' or 'peak'
  # used to pass into riemann event message below 
end 
 
c << {
  tag: 'push_notification',
  state: "#{define_state}",
  service: 'PushNotifications',
  metric: "#{do_something_to_get_metric}",
  description: "Push notificaiton sent",
  time: Time.now.to_i - 10
}
```

The Rieman server stream snippet that might deal with states events defined as 'peak' by the define_state() function in the Ruby above
In this stream I want to make sure when in a 'peak' state weare pushing 8000 to 10000 notications based on metric provided.

```clojure
(streams
  (default :ttl 60
    ; Index all events immediately.
    index
    ; Log expired events.
    (expired
      (fn [event] (info "expired" event))))
    (where (service "PushNotifications")
      (where (state "peak")
        (where (<= 8000 metric 10000)
          (with :state "ok" index)
            (else
              (with :state "warning" index) tell-team ))))
```

