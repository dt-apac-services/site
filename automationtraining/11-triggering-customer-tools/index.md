## Triggering Customer Tools

The initial and probably most natural thought will be to integrate Keptn with a tool (for example Jenkins). So you'll probably want to trigger a quality gate evaluation FROM jenkins. Then have Jenkins wait for the result before proceeding.

Although it would work, you'd miss the strength that Keptn brings in the orchestration and so the best practice is actually flip the whole model on it's head. You trigger a Keptn sequence. A Keptn service (either the webhook service or something like the job executor) then triggers Jenkins. Jenkins is now only responsible for signalling that it is done (by sending back a `finished` event). Then Keptn proceeds with the sequence and, as we know, the qualtiy evaluation is a native task in Keptn.

> The advantage here is obvious: Minimal changes to the customer Jenkins pipeline and yet they get all the benefits of Keptn as an orchestration layer.

Here are some boilerplate examples and architecture diagrams. Note: These will eventually be moved to the [Keptn Integrations]() page: `https://github.com/agardnerIT/keptn-integration-boilerplate`