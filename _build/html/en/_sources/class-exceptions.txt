.. Stub file
   Added: September 7th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

==========
Exceptions
==========

This document contains the descriptions and throw cases for all exceptions in the client. Use this reference when you're unsure why you may have gotten an exception.

DiffbotException
================

.. php:namespace:: Swader\Diffbot\Exceptions

.. php:exception:: DiffbotException

The DiffbotException is an empty exception class that extends the base PHP ``\Exception``. It is the base for all other Diffbot exceptions - though currently, it is the only one.

Its main current purpose is to let the user know that something went wrong with the client, not its dependencies or the application consuming it.