IPv6 Datagram Reassembly
========================

:mod:`pcapkit.reassembly.ipv6` contains
:class:`~pcapkit.reassembly.ipv6.IPv6_Reassembly`
only, which reconstructs fragmented IPv6 packets back to
origin. Please refer to :doc:`ip` for more information.

Data Structure
--------------

.. glossary::

   ipv6.packet
      Data structure for **IPv6 datagram reassembly**
      (:meth:`~pcapkit.reassembly.reassembly.Reassembly.reassembly`)
      is as following:

      .. code:: python

         packet_dict = dict(
           bufid = tuple(
               ipv6.src,                   # source IP address
               ipv6.dst,                   # destination IP address
               ipv6.label,                 # label
               ipv6_frag.next,             # next header field in IPv6 Fragment Header
           ),
           num = frame.number,             # original packet range number
           fo = ipv6_frag.offset,          # fragment offset
           ihl = ipv6.hdr_len,             # header length, only headers before IPv6-Frag
           mf = ipv6_frag.mf,              # more fragment flag
           tl = ipv6.len,                  # total length, header includes
           header = ipv6.header,           # raw bytearray type header before IPv6-Frag
           payload = ipv6.payload,         # raw bytearray type payload after IPv6-Frag
         )

   ipv6.datagram
      Data structure for **reassembled IPv6 datagram** (element from
      :attr:`~pcapkit.reassembly.reassembly.Reassembly.datagram` *tuple*)
      is as following:

      .. code:: python

         (tuple) datagram
          |--> (dict) data
          |     |--> 'NotImplemented' : (bool) True --> implemented
          |     |--> 'index' : (tuple) packet numbers
          |     |               |--> (int) original packet range number
          |     |--> 'packet' : (Optional[bytes]) reassembled IPv6 packet
          |--> (dict) data
          |     |--> 'NotImplemented' : (bool) False --> not implemented
          |     |--> 'index' : (tuple) packet numbers
          |     |               |--> (int) original packet range number
          |     |--> 'header' : (Optional[bytes]) IPv6 header
          |     |--> 'payload' : (Optional[tuple]) partially reassembled IPv6 payload
          |                       |--> (Optional[bytes]) IPv4 payload fragment
          |--> (dict) data ...

   ipv6.buffer
      Data structure for internal buffering when performing reassembly algorithms
      (:attr:`~pcapkit.reassembly.reassembly.Reassembly._buffer`) is as following:

      .. code:: python

         (dict) buffer --> memory buffer for reassembly
          |--> (tuple) BUFID : (dict)
          |     |--> ipv6.src       |
          |     |--> ipc6.dst       |
          |     |--> ipv6.label     |
          |     |--> ipv6_frag.next |
          |                         |--> 'TDL' : (int) total data length
          |                         |--> RCVBT : (bytearray) fragment received bit table
          |                         |             |--> (bytes) b'\x00' -> not received
          |                         |             |--> (bytes) b'\x01' -> received
          |                         |             |--> (bytes) ...
          |                         |--> 'index' : (list) list of reassembled packets
          |                         |               |--> (int) packet range number
          |                         |--> 'header' : (bytearray) header buffer
          |                         |--> 'datagram' : (bytearray) data buffer, holes set to b'\x00'
          |--> (tuple) BUFID ...

Implementation
--------------

.. automodule:: pcapkit.reassembly.ipv6
   :members:
   :undoc-members:
   :private-members:
   :show-inheritance:
