# Pivot Stream Events

During a stream, Pivot will publish some events which your application can subscribe to, using either
a WebSocket, to receive them in real-time via [Blackhole](https://docs.2600hz.com/git/2600hz/kazoo-blackhole/doc/README.md),
or through invoking HTTP request to your application [Weboooks](https://docs.2600hz.com/git/2600hz/kazoo-webhooks/doc/README.md).

## Receiving Pivot stream events in real-time

If you need to monitor stream events in real-time in your application,
your application can subscribe to Blackhole WebSocket event to receive Pivot stream events.

When a stream event happens, Pivot will publish that event to Blackhole, and Blackhole will send the event to
any connection that subscribed to receive the Pivot stream events.

For further info see Blackhole [Pivot Stream](https://docs.2600hz.com/git/2600hz/kazoo-blackhole/doc/api/events/bh_stream.md).

## Receiving Pivot stream events via HTTP request

Webhooks provides a way to invoke HTTP request to your application for Pivot stream.

You need first use Webhooks from portal or use [Webhooks API](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/webhooks.md) to
create a hook. When creating the hook you can set the type to [Pivot Stream](https://docs.2600hz.com/git/2600hz/kazoo-webhooks/doc/events/stream.md).

When a stream starts or stopped, Pivot will publish the event to Webhooks application to invoke a HTTP request to your application.
