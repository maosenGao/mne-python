
.. contents:: Contents
   :local:
   :depth: 3

.. _memory:

Memory-efficient IO
###################

Preloading
==========

Raw
^^^
MNE-Python can read data on-demand using the ``preload`` option provided in raw reading functions. For example::

    from mne import io
    from mne.datasets import sample
    data_path = sample.data_path()
    raw_fname = data_path + '/MEG/sample/sample_audvis_filt-0-40_raw.fif'
    raw = io.read_raw_fif(raw_fname, preload=False)

.. note:: Filtering, resampling and dropping or selecting channels does not work with ``preload=False``.

Epochs
^^^^^^
Similarly, epochs can also be be read from disk on-demand. For example::

    import mne
    events = mne.find_events(raw)
    event_id, tmin, tmax = 1, -0.2, 0.5
    picks = mne.pick_types(raw.info, meg=True, eeg=True, stim=False, eog=True)
    epochs = mne.Epochs(raw, events, event_id, tmin, tmax, picks=picks, baseline=(None, 0), reject=dict(eeg=80e-6, eog=150e-6),
                        preload=False)

When ``preload=False``, the epochs data is loaded from the disk on-demand. Note that ``preload=False`` for epochs will work even if the ``raw`` object
has been loaded with ``preload=True``. Preloading is also supported for :func:`mne.read_epochs`.

.. warning:: This comes with a caveat. When ``preload=False``, data rejection based on peak-to-peak thresholds is executed when the data is loaded from disk, *not* when the ``Epochs`` object is created.

To explicitly reject artifacts with ``preload=False``, use the function :func:`mne.Epochs.drop_bad`.

Loading data explicitly
=======================
To load the data if ``preload=False`` was initially selected, use the functions :func:`mne.io.Raw.load_data` and :func:`mne.Epochs.load_data`.

Simplest way to access data
===========================
If you just want your raw data as a numpy array to work with it in a different framework you can use slicing syntax::

    first_channel_data, times = raw[0, :]
    channel_3_5, times_3_5 = raw[3:5, :]
