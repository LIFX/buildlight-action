# LIFX Buildlight Action

This GitHub Action is built to allow you to indicate the status of a build on a LIFX device.

## Usage

This action allows you to represent three states of build, `in_progress`, `success` and `failure`. It is intended that at the start of the build you trigger the `in_progress` state, then once the build status has been decided you set it to either `success` or `failure`. You can use the [`job` context](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/contexts-and-expression-syntax-for-github-actions#job-context) to gather this information automatically.

Such an example workflow might look like this:

```yaml
steps:
- uses: LIFX/buildlight-action@master
  with:
    lifx-token: ${{ secrets.LIFX_TOKEN }}
    selector: 'location:Work'
    status: 'in_progress'
- uses: actions/checkout@v1
- uses: actions/setup-node@v1
  with:
    node-version: '10.x'
- run: npm install
- run: npm test
- uses: LIFX/buildlight-action@master
  if: always()
  with:
    lifx-token: ${{ secrets.LIFX_TOKEN }}
    selector: 'location:Work'
    status: ${{ job.status }}
```

## Options

This action _requires_ the following arguments.

### lifx-token

The `lifx-token` argument must be a valid LIFX personal access token. You can get a personal access token by going to [https://cloud.lifx.com/settings](https://cloud.lifx.com/settings), logging in, and creating a token. More information can be found on our [API documentation usage page](https://api.developer.lifx.com/docs/how-to-use-the-following-examples).

### selector

The selector allows you to select which devices will react to the pipeline. Using selectors you can change a single light, a single zone on a strip or beam, a group, a location or your entire account. You can read more about selectors on the [API documentation selectors page](https://api.developer.lifx.com/docs/selectors).

Some example selectors are:

* `all` - All lights on the account.
* `label:Build` - All lights labelled 'build'.
* `location:Home` - All lights in the location named 'Home'.
* `id:d073d52988f3` - The light with the serial number `d073d52988f3`.
* `id:d073d52988f3|5` - The light zone `5` on the device with the serial number `d073d52988f3`.

### status

Status can be set to one of `in_progress`, `success`, `failure`, `cancelled`, `timed_out`, `neutral`, `timed_out` or `action_required`. This will cause the lights to change status. Each status appears as follows:

#### in_progress, neutral

The selected lights will 'breathe' slowly between the previous state and orange. This will continue either for 30 minutes, or until another change is made.

#### success

The selected lights will turn green.

#### failure, timed_out, action_required

The selected lights will turn red.

#### cancelled

The selected lights will turn orange.
